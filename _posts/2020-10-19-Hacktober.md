---
layout: post
title: "Hacktober 2020 CTF - Red Rum"
date: 2020-10-19
categories: CTF
permalink: /Hacktober
---
##Hacktober 2020 CTF - Red Rum

We want you to infiltrate DEADFACE as a programmer. Thing is, they're picky about who they bring in. They want to make sure you're the real deal when it comes to programming. Generate a list of numbers 1-500. For each number divisible by 3, replace it with Red; for each number divisible by 5, replace it with Rum. For numbers divisible by both 3 AND 5, replace it with RedRum.

`nc env2.hacktober.io 5000`

This is a programming challenge from the Hacktober 2020 CTF brought to you by CyberUp, Cyber Hacktics, and United States Air Force veterans in support of National Cyber Security Awareness Month. I'm not usually one to attempt programming challenges, but this one seemed easy enough to me that I would give it a shot. The following is what I came up with to get the flag.

I first wrote a program in Python.

```
myList = []

for x in range(1, 501):
        if x % 3 == 0 and x % 5 == 0:
                myList.append("RedRum")
        elif x % 3 == 0:
                myList.append("Red")
        elif x % 5 == 0:
                myList.append("Rum")
        else:
                myList.append(str(x))

jointed = ",".join(myList)
print(jointed)
```

There must have been something wrong with the server at the time I wrote this, because I could not get the flag, even though my output was correct. Since they are leaving the server up after the CTF for a few weeks, I ran the code again, and it now outputs the flag.

```
kali@kali:~$ python3 deadface.py | nc env2.hacktober.io 5000
DEADFACE gatekeeper: If you want to join our programmers circle, you need to
show that you can at least do the basics. Send the first 500 (1 - 500) Red Rums
to show you're serious. You answer should be comma-separated with no spaces.

flag{h33eeeres_j0hnny!!!}
```

Since I've been learning Go recently, I was kind of bummed with myself for just writing the program in Python right away. I rewrote it in Go the next day just to see if I could. It was much easier for me to write it and I definitely think Go is the programming language I will stick with for the future.

```
package main

import "fmt"

func main() {
	for i:=1; i<501; i++ {
		if i % 3 == 0 && i % 5 ==0 {
			fmt.Print(",RedRum")
		} else if i % 3 == 0 {
			fmt.Print(",Red")
		} else if i % 5 == 0 {
			fmt.Print(",Rum")
		} else if i == 1 {
			fmt.Print(i)
		} else {
			fmt.Print(",",i)
		}
	}
	fmt.Print("\n")
}

mf@ThinkPad:~/golang$ go build deadface.go
mf@ThinkPad:~/golang$ ./deadface | nc env2.hacktober.io 5000
DEADFACE gatekeeper: If you want to join our programmers circle, you need to
show that you can at least do the basics. Send the first 500 (1 - 500) Red Rums
to show you're serious. You answer should be comma-separated with no spaces.

flag{h33eeeres_j0hnny!!!}
```

This was a really great refresher on some programming basics like if/else if statements and modulus operators. Overall if was a great CTF and I look forward to doing more from Cyber Hacktics in the future.
