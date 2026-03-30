# unpackme Writeup - picoCTF

**Category:** Reverse Engineering  
**Difficulty:** Medium

This writeup describes the solution to the **"unpackme"** challenge from picoCTF.

The goal of the challenge is to unpack a compressed binary and reverse engineer it in order to retrieve the flag.

---

## Step 1 – Downloading the Challenge

I opened the picoCTF challenge and downloaded the provided files.

![Challenge page](screenshots/01-challenge-page.png)

---

## Step 2 – First Attempt at Probing

![Initial attempt](screenshots/02-initial-attempt.png)

Providing a simple input such as `"111"` does not reveal the flag, indicating that further analysis is required.

When opening the binary in **IDA**, the code appears incomplete. This suggests that the binary might be packed or compressed.

![Ida-packed-binary](screenshots/03-ida-packed-binary.png)

---

## Step 3 – Unpacking the file

![Upx test output](screenshots/04-upx-test-output.png)

The issue is that the binary is packed using UPX.

To unpack it, we run:

`upx -d unpackme-upx`

![Upx unpack output](screenshots/05-upx-unpack-output.png)

After unpacking, the binary can be fully analyzed in IDA.

![Ida unpacked binary](screenshots/06-ida-unpacked-binary.png)

---

## Step 4 – Reverse engineering the binary file

After analyzing the `main` function, we can identify user input handling using `scanf`.

![Scanf call disassembly](screenshots/07-scanf-call-disassembly.png)

The program reads user input using `scanf` and stores it at `[rbp + var_3c]`.

Immediately after, the input value is moved into `eax` and compared against the constant `0x0B83CB`. If the input matches this value, execution jumps to the success branch, which prints the flag.

![Input comparison assembly](screenshots/08-input-comparison-assembly.png)

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
