---
layout: post
title: "redpwnCTF - Bubbly"
date: 2020-07-01
categories: CTF
permalink: /redpwnCTF
---
redpwnCTF - Bubbly

I was given a reverse engineering tutorial by teammate reloc for this challenge. I did not keep my
specific notes during the tutorial, as I was trying to follow along and learn what was going on. I
kept the challenge file and after waiting two weeks after the CTF ended, let's see what I can remember
about using gdb. The following differs a lot from what we did that first night we solved this. Reloc
rewrote a portion of the program in python as we went along so that he could show me what was going on
with the assembly code. I am not that good at coding and will be using a simpler method of setting
a breakpoint in a for loop to examine what changes are happening as we go along.

First, let's run the program to see what we can find out.
```
(gdb) run
Starting program: /root/Downloads/bubbly
I hate my data structures class! Why can't I just sort by hand?
7
3
4
9
Try again!
[Inferior 1 (process 2577) exited normally]
```
The program is taking a numeric input between 0 and 8, and anything higher exits the function. Let's
set the syntax to intel and disassemble the main function.
```
(gdb) set disassembly-flavor intel
(gdb) disass main
Dump of assembler code for function main:
   0x00005555555551d9 <+0>: 	push   rbp
   0x00005555555551da <+1>: 	mov	rbp,rsp
   0x00005555555551dd <+4>: 	sub	rsp,0x10
   0x00005555555551e1 <+8>: 	mov	rax,QWORD PTR [rip+0x2eb8]    	# 0x5555555580a0 
   0x00005555555551e8 <+15>:	mov	esi,0x0
   0x00005555555551ed <+20>:	mov	rdi,rax
   0x00005555555551f0 <+23>:	call   0x555555555040 
   0x00005555555551f5 <+28>:	mov	rax,QWORD PTR [rip+0x2eb4]    	# 0x5555555580b0 
   0x00005555555551fc <+35>:	mov	esi,0x0
   0x0000555555555201 <+40>:	mov	rdi,rax
   0x0000555555555204 <+43>:	call   0x555555555040 
   0x0000555555555209 <+48>:	mov	rax,QWORD PTR [rip+0x2eb0]    	# 0x5555555580c0 
   0x0000555555555210 <+55>:	mov	esi,0x0
   0x0000555555555215 <+60>:	mov	rdi,rax
   0x0000555555555218 <+63>:	call   0x555555555040 
   0x000055555555521d <+68>:	lea	rdi,[rip+0xdf4]    	# 0x555555556018
   0x0000555555555224 <+75>:	call   0x555555555030 
   0x0000555555555229 <+80>:	mov	BYTE PTR [rbp-0x1],0x0
=> 0x000055555555522d <+84>:	lea	rax,[rbp-0xc]
   0x0000555555555231 <+88>:	mov	rsi,rax
   0x0000555555555234 <+91>:	lea	rdi,[rip+0xe1d]    	# 0x555555556058
   0x000055555555523b <+98>:	mov	eax,0x0
   0x0000555555555240 <+103>:   call   0x555555555060 <__isoc99_scanf@plt>
   0x0000555555555245 <+108>:   mov	DWORD PTR [rbp-0x8],eax
   0x0000555555555248 <+111>:   mov	eax,DWORD PTR [rbp-0xc]
   0x000055555555524b <+114>:   cmp	eax,0x8
   0x000055555555524e <+117>:   ja 	0x55555555534d 
   0x0000555555555254 <+123>:   mov	eax,DWORD PTR [rbp-0xc]
   0x0000555555555257 <+126>:   mov	eax,eax
   0x0000555555555259 <+128>:   lea	rdx,[rax*4+0x0]
   0x0000555555555261 <+136>:   lea	rax,[rip+0x2df8]    	# 0x555555558060 
   0x0000555555555268 <+143>:   mov	edx,DWORD PTR [rdx+rax*1]
   0x000055555555526b <+146>:   mov	eax,DWORD PTR [rbp-0xc]
   0x000055555555526e <+149>:   add	eax,0x1
   0x0000555555555271 <+152>:   mov	eax,eax
   0x0000555555555273 <+154>:   lea	rcx,[rax*4+0x0]
   0x000055555555527b <+162>:   lea	rax,[rip+0x2dde]    	# 0x555555558060 
   0x0000555555555282 <+169>:   mov	eax,DWORD PTR [rcx+rax*1]
   0x0000555555555285 <+172>:   mov	esi,DWORD PTR [rbp-0xc]
   0x0000555555555288 <+175>:   mov	ecx,edx
   0x000055555555528a <+177>:   xor	ecx,eax
   0x000055555555528c <+179>:   mov	eax,esi
   0x000055555555528e <+181>:   lea	rdx,[rax*4+0x0]
   0x0000555555555296 <+189>:   lea	rax,[rip+0x2dc3]    	# 0x555555558060 
   0x000055555555529d <+196>:   mov	DWORD PTR [rdx+rax*1],ecx
   0x00005555555552a0 <+199>:   mov	eax,DWORD PTR [rbp-0xc]
   0x00005555555552a3 <+202>:   add	eax,0x1
   0x00005555555552a6 <+205>:   mov	eax,eax
   0x00005555555552a8 <+207>:   lea	rdx,[rax*4+0x0]
   0x00005555555552b0 <+215>:   lea	rax,[rip+0x2da9]    	# 0x555555558060 
   0x00005555555552b7 <+222>:   mov	edx,DWORD PTR [rdx+rax*1]
   0x00005555555552ba <+225>:   mov	eax,DWORD PTR [rbp-0xc]
   0x00005555555552bd <+228>:   mov	eax,eax
   0x00005555555552bf <+230>:   lea	rcx,[rax*4+0x0]
   0x00005555555552c7 <+238>:   lea	rax,[rip+0x2d92]    	# 0x555555558060 
   0x00005555555552ce <+245>:   mov	eax,DWORD PTR [rcx+rax*1]
   0x00005555555552d1 <+248>:   mov	ecx,DWORD PTR [rbp-0xc]
   0x00005555555552d4 <+251>:   lea	esi,[rcx+0x1]
   0x00005555555552d7 <+254>:   mov	ecx,edx
   0x00005555555552d9 <+256>:   xor	ecx,eax
   0x00005555555552db <+258>:   mov	eax,esi
   0x00005555555552dd <+260>:   lea	rdx,[rax*4+0x0]
   0x00005555555552e5 <+268>:   lea	rax,[rip+0x2d74]    	# 0x555555558060 
   0x00005555555552ec <+275>:   mov	DWORD PTR [rdx+rax*1],ecx
   0x00005555555552ef <+278>:   mov	eax,DWORD PTR [rbp-0xc]
   0x00005555555552f2 <+281>:   mov	eax,eax
   0x00005555555552f4 <+283>:   lea	rdx,[rax*4+0x0]
   0x00005555555552fc <+291>:   lea	rax,[rip+0x2d5d]    	# 0x555555558060 
   0x0000555555555303 <+298>:   mov	edx,DWORD PTR [rdx+rax*1]
   0x0000555555555306 <+301>:   mov	eax,DWORD PTR [rbp-0xc]
   0x0000555555555309 <+304>:   add	eax,0x1
   0x000055555555530c <+307>:   mov	eax,eax
   0x000055555555530e <+309>:   lea	rcx,[rax*4+0x0]
   0x0000555555555316 <+317>:   lea	rax,[rip+0x2d43]    	# 0x555555558060 
   0x000055555555531d <+324>:   mov	eax,DWORD PTR [rcx+rax*1]
   0x0000555555555320 <+327>:   mov	esi,DWORD PTR [rbp-0xc]
   0x0000555555555323 <+330>:   xor	edx,eax
   0x0000555555555325 <+332>:   mov	ecx,edx
   0x0000555555555327 <+334>:   mov	eax,esi
   0x0000555555555329 <+336>:   lea	rdx,[rax*4+0x0]
   0x0000555555555331 <+344>:   lea	rax,[rip+0x2d28]    	# 0x555555558060 
   0x0000555555555338 <+351>:   mov	DWORD PTR [rdx+rax*1],ecx
   0x000055555555533b <+354>:   mov	eax,0x0
   0x0000555555555340 <+359>:   call   0x555555555165 
   0x0000555555555345 <+364>:   mov	BYTE PTR [rbp-0x1],al
   0x0000555555555348 <+367>:   jmp	0x55555555522d 
   0x000055555555534d <+372>:   nop
   0x000055555555534e <+373>:   cmp	BYTE PTR [rbp-0x1],0x0
   0x0000555555555352 <+377>:   je 	0x55555555536c 
   0x0000555555555354 <+379>:   lea	rdi,[rip+0xd00]    	# 0x55555555605b
   0x000055555555535b <+386>:   call   0x555555555030 
   0x0000555555555360 <+391>:   mov	eax,0x0
   0x0000555555555365 <+396>:   call   0x5555555551bf 
   0x000055555555536a <+401>:   jmp	0x555555555378 
   0x000055555555536c <+403>:   lea	rdi,[rip+0xcf3]    	# 0x555555556066
   0x0000555555555373 <+410>:   call   0x555555555030 
   0x0000555555555378 <+415>:   mov	eax,0x0
   0x000055555555537d <+420>:   leave  
   0x000055555555537e <+421>:   ret    
End of assembler dump.
```
The part of the disassembled code that jumps out to me is at <+103>, where we see the scanf function
listed. That is the area of the program that we are entering our values. The code below that takes our
input and begins to pull values from an array and swap them around and put them back into the array. The
program will loop through this function until we enter a number over 8. We can see the
numbers in the array by using display nums.
```
(gdb) disp nums
1: nums = {1, 10, 3, 2, 5, 9, 8, 7, 4, 6}
```
If we set a breakpoint before we enter each value, we can see the changes to the array, and enter
our next value accordingly.
```
(gdb) break 38
Breakpoint 3 at 0x55555555522d: file main.c, line 38.
(gdb) run
Starting program: /root/Downloads/bubbly
I hate my data structures class! Why can't I just sort by hand?

Breakpoint 3, main () at main.c:38
38  	in main.c
1: nums = {1, 10, 3, 2, 5, 9, 8, 7, 4, 6}
(gdb) c
Continuing.
1

Breakpoint 3, main () at main.c:38
38  	in main.c
1: nums = {1, 3, 10, 2, 5, 9, 8, 7, 4, 6}
(gdb) c
Continuing.
2

Breakpoint 3, main () at main.c:38
38  	in main.c
1: nums = {1, 3, 2, 10, 5, 9, 8, 7, 4, 6}
(gdb)
```
You can see after we begin running the program it will break before the input and display the nums
array to us. We use "c" to continue and then enter our value. The array starts at value 0, so we can use
the values 0-8 to swap all of the values in the array. From this point there are a lot of ways you can
go about this, but the final goal is to have all the values in order. Entering a value over 8 enters
the check to see if they are in order or not. If they are, the program will run the print_flag function
and give us the flag.
```
Breakpoint 3, main () at main.c:38
38  	in main.c
1: nums = {1, 2, 3, 5, 4, 6, 7, 8, 9, 10}
(gdb) c
Continuing.
3

Breakpoint 3, main () at main.c:38
38  	in main.c
1: nums = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
(gdb) c
Continuing.
9
Well done!
[Detaching after vfork from child process 2681]
cat: flag.txt: No such file or directory
[Inferior 1 (process 2621) exited normally]
```
As long as we take note of the order that we used to solve it, all we have to do is netcat over
to their server and enter the same string of digits. I used the following: 1 2 3 4 5 6 7 8 4 5 6 7 1 4 5 6 4 5 3
```
$ nc 2020.redpwnc.tf 31039
I hate my data structures class! Why can't I just sort by hand?
1
2
3
4
5
6
7
8
4
5
6
7
1
4
5
6
4
5
3
9
Well done!
Flag{4ft3r_y0u_put_u54c0_0n_y0ur_c011ege_4pp5_y0u_5t1ll_h4ve_t0_d0_th15_57uff}
```
This challenge was a fantastic introduction to reverse engineering and gdb. I cannot thank Reloc enough
for his assistance and I can't wait for my next opportunity to learn some more reverse engineering.
