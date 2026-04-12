# anomalous-array Writeup - pwn.college

**Category:** Memory Corruption  
**Difficulty:** Hard

This writeup describes the solution to the **"anomalous-array"** challenge from pwn.college.  
The goal is ...

---

## Step 1 – First Attempt at Probing

![Initial Attempt](screenshots/01-initial-attempt.png)

The program expects to number to view. when I choose 1, I saw that the hacker number is 1337c0de. What will happen with another number as an input?

![Second Attempt To Understand](screenshots/02-second-attempt-to-understand.png)

We will try to enter the standings through DGB.

---

## Step 2 – Observation via GDB

So what we see in Gdb?

![](screenshots/03.png)

These lines This print: "Which number would you like to view?"

![](screenshots/04.png)

After we insert the input with `scanf` function, we see that its take the input (as number) and calculate the hacker number to print:

![](screenshots/05.png)

That is, we have an array that starts at the address: $rbp+8*0xAE-0x7F8 (which is actually: rbp - 0x288), and we enter an index as input. The hacker number that is printed is the array in place of the index we entered.
This is nice, but where I can find the flag? let's take view about what happen before:

![](screenshots/06.png)
![](screenshots/07.png)

Beatuiful! This is open the "flag" file, and store the flag on the stack (at rbp - 0x10). But rbp - 0x10 is only a **pointer** to the palce that the flag stored. Where the flag itself is located?

![](screenshots/08.png)

Exactly here - at rbp - 0x800!

---

## Step 3 – Identifying the Vulnerability

Conclusion from the gdb:
the start of the array - rbp - 0x288
the place of the flag - rbp - 0x800 (above to the array)
so, a regular index will not help to arraive to the flag, but if we insert as an input a nagetive number?
The negative index that will lead me to the tart of the flag is: - (0x88 - 0x288) / 8 = -0xAF (-175 in dec).
let's try it:

![](screenshots/09.png)

---

## Step 4 – Exploitation Strategy

When I examine the printf funtion of the hacker number I duscover 2 problems:

![](screenshots/10.png)

1. The format is unsigned long, and I want to print all the flag, not only the 8 first bytes.
2. The format is hexadecimal, and I want to convert it to string format for see the flag.

So, I want to build **python** script that will solve these problems - its will execuate the program 32 times (because the length of the flag is maxsimum 0x100, and in each time this prints 8 bytes), and in each time I convert this ti ascii. In the end, I will print the Unification of all chunks of the flag.

![](screenshots/11.png)

---

## Step 5 –  Retrieving the Flag

I execuated this code and after 32 times got the complete 
flag:

![Exploit success Flag Output](screenshots/13-exploit-success-flag-output.png)

The successful exploitation confirms full control over the program’s execution flow.

![Flag Submission Confirmation](screenshots/14-flag-submission-confirmation.png)

---

## Summary and Insights

A classic buffer overflow allows overwriting the **saved return address**, redirecting execution to a chosen function such as `win()`.

In this challenge, however, a size check prevents sending a payload large enough to reach the return address. To bypass this restriction, we exploit a **32-bit integer overflow** in the size calculation.

By supplying carefully chosen input values, the multiplication overflows and produces a truncated result that passes the check, while still allowing us to send a much larger payload in practice.

This demonstrates how unsafe arithmetic operations can undermine security checks, ultimately enabling control flow hijacking.

