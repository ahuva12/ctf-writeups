# keygenme Writeup - picoCTF

**Category:** Reverse Engineering  
**Difficulty:** Hard

This writeup describes the solution to the **"unpackme"** challenge from picoCTF.

The goal of the challenge is to unpack a compressed binary and reverse engineer it in order to retrieve the flag.

---

## Step 1 – Downloading the Challenge

I opened the picoCTF challenge and downloaded the provided files.

![Challenge page](screenshots/01-challenge-page.png)

---

## Step 2 – First Attempt at Probing

![Initial attempt](screenshots/02-initial-attempt.png)

Providing a simple input such as `"111"` does not reveal the valid key, indicating that further analysis is required.

When opening the binary in **IDA**, the code appears incomplete. This suggests that the binary might be packed or compressed.

![Ida-packed-binary](screenshots/03-ida-packed-binary.png)

---

## Step 3 – Strat of Research

![Upx test output](screenshots/04-upx-test-output.png)

The fgets function requests input from the user. It sends it to the "checking_input" function. For this will print "That is vaild" - I need that this function will return not zeru value. Let's dive into the checking_input function. I changed the name for the convenience of reverse engineering.

---

## Step 4 – Reverse engineering checking_input function

These are the possible return values ​​for the function. There is only one possibility that it will return a value other than 0.

![Scanf call disassembly](screenshots/07-scanf-call-disassembly.png)

Now we will try to understand how to make the checking_input function return 1.

![Scanf call disassembly](screenshots/07-scanf-call-disassembly.png)

First thing – store the input in rbp+var_D8. This is very important for further understanding.

We'll see you next time we meet the input:

![Scanf call disassembly](screenshots/07-scanf-call-disassembly.png)

The strlen function tells us that the input must be exactly 0x24 (36 bytes) long.

---

## Step 5 – Retrieving the Flag

Converting `0x0B83CB` to decimal gives `754635`.

Providing this input results in successful execution and reveals the flag.

![Successful execution flag](screenshots/09-successful-execution-flag.png)

The challenge confirms that the exploit worked successfully.

![PicoCTF flag submission](screenshots/10-picoCTF-flag-submission.png)

---

## Summary and Insights

This challenge demonstrates the importance of **identifying packed binaries and properly unpacking** them before performing reverse engineering.

Once unpacked, **standard analysis tools** such as IDA can be used to trace program logic and identify the required input.
