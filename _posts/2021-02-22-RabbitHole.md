---
layout: post
title: "Tenable CTF - Down the Rabbit Hole"
date: 2021-02-22
categories: CTF
permalink: /RabbitHole
---

Tenable CTF - Follow the Rabbit Hole

Follow the rabbit hole and get the flag.

Visiting one of the urls from the challenge will take us to http://167.71.246.232:8080/rabbit_hole.php?page=cE4g5bWZtYCuovEgYSO1 which displays the following information.

`[513, '71'] 4O48APmBiNJhZBfTWMzD`

The last string looks similar to the page parameter in the first url, so let's try visiting that.
```
http://167.71.246.232:8080/rabbit_hole.php?page=4O48APmBiNJhZBfTWMzD
[803, 'A5'] dUfob5k9t2vH1dVEU9bU
http://167.71.246.232:8080/rabbit_hole.php?page=dUfob5k9t2vH1dVEU9bU
[371, '08'] EiFCRRS86AT19seqH1ls
and on and on...
```
So we can see we are given a number, a hex byte and a string to the next page.  We can write some Go code to visit each page and capture the data. We will store the data in an array, storing each hex byte at the corresponding point in the array to the number before it. When you go far enough down the rabbit hole, you will arrive at a page that simply says "end," so we will adjust our Go code to watch for this and stop at that point. 
```
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"strconv"
	"strings"
)

func main() {
	base := "http://167.71.246.232:8080/rabbit_hole.php?page="
	start := "cE4g5bWZtYCuovEgYSO1"
	newurl := base + start

	hex := [1582]string{} //create array to store hex characters in correct order

	for i := 0; i < 1590; i++ {
		response, err := http.Get(newurl)
		if err != nil {
			log.Fatal(err)
		}
		defer response.Body.Close()

		newret, err := ioutil.ReadAll(response.Body)
		if err != nil {
			log.Fatal(err)
		}

		//Split string into decimal and hex, followed by string to next url
		a := strings.Split(string(newret), "\n")
		if a[0] == "end" {
			break
		}

		//Remove excess characters and split the string into the decimal and hex
		b := strings.ReplaceAll(a[0], "[", "")
		b = strings.ReplaceAll(b, "]", "")
		b = strings.ReplaceAll(b, "'", "")
		c := strings.Split(b, ",")
		d, _ := strconv.Atoi(c[0])

		//Store hex in correct position in array
		hex[d] = c[1]

		//remove leading space from next url and add it to the the base url
		next := strings.Replace(a[1], " ", "", 1)
		newurl = base + next
	}

	//print out final hex string
	for i := 0; i < len(hex); i++ {
		fmt.Printf(hex[i])
	}
}
```
We pipe the output of the program into a file called rabbit.hex, and then we can use xxd to convert the hex back into its original binary form.  We run file on that output and find out it's a PNG file.  We've got the flag!
```
kali@kali:~/golang/rabbit$ xxd -r -p rabbit.hex > rabbit.png
kali@kali:~/golang/rabbit$ file rabbit.png
rabbit.png: PNG image data, 281 x 118, 8-bit/color RGB, non-interlaced
```
![Rabbit Hole Flag](/images/rabbit.png)
