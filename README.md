Cracking a Password-Protected PDF with John the Ripper
A walkthrough of extracting a crackable hash from a password-protected PDF and recovering the password using John the Ripper (Johnny GUI), completed as part of a hands-on lab from Networkwalks Academy. Done in an isolated Windows 11 VM (VirtualBox) against a PDF provided for this exercise.
> ⚠️ **Scope note:** This was performed against a training file I was explicitly given for practice. Don't run these steps against PDFs or systems you don't own or have permission to test.
Goal
Recover the open-password of an encrypted PDF (`My Locked PDF1 (for NW Tools Lab WK3-PM2).pdf`, 65.2 KB) using only the hash extracted from the file — no brute-forcing the UI, no guessing.
Tools used
Johnny (GUI front-end for John the Ripper) — `John the Ripper 1.9.0-jumbo-1`
Online HashCrack's PDF Hash Extractor (produces a `pdf2john`/hashcat-compatible hash)
Networkwalks' Hash Calculator and Password Cracker (browser-based tools, used as an independent second method to confirm the result)
Windows 11 VM in Oracle VirtualBox (isolated lab environment)
Steps
1. Point Johnny to the John the Ripper binary
Configured Johnny's Settings tab to use the jumbo build of John the Ripper (needed for the wider range of supported hash formats, including PDF). Johnny confirmed it detected `John the Ripper 1.9.0-jumbo-1 OMP`:
![Step 1 - Johnny settings pointing to john.exe](screenshort%201.png)
2. Extract a crackable hash from the PDF
Uploaded the locked PDF to Online HashCrack's PDF hash extractor tool. Files are processed for extraction only and deleted immediately afterward — nothing is retained server-side:
![Step 2 - Uploading the PDF to the hash extractor](screenshot%202.png)
3. Save the hash to a file
Saved the extracted hash as `hash1.txt`, ready for John the Ripper's `--format=PDF` mode:
![Step 3 - Saving the hash to hash1.txt in Notepad](screenshot%203.png)
```
Revision: R4   Version: V4   Key length: 128-bit

$pdf$4*4*128*-1060*1*16*55d1a5c14175da449753199e44971d32*32*777fd021a7f3c5ae598c0c6495c7f76e0000000000000000000000000000000*32*ceecdac74b19b5a62688d3b3524e1374c955cbb9cc3c45316494d9446ef81af1
```
4. Load the hash and run the attack
Loaded `hash1.txt` into Johnny and ran the built-in wordlist attack against it. It cracked instantly, reporting 100% (1/1 cracked, 0 left):
![Step 4 - Johnny showing the password cracked](screenshot%204.png)
Recovered password: `password1`
5. Unlock the PDF
Entered the recovered password at the original prompt:
![Step 5 - Entering the recovered password](screenshot%205.png)
6. Confirm success
The file opened, confirming a correct crack, and revealed the lab's completion flag:
![Step 6 - PDF unlocked, flag revealed](screenshot%206.png)
```
nw{networkwalks_flag1_jtr_...}
```
7. Cross-check with Networkwalks' Hash Calculator
As an independent second method, I ran the same PDF through Networkwalks' own Hash Calculator tool, which also extracts a `pdf2john`-compatible hash directly in the browser:
![Step 7 - Networkwalks Hash Calculator tool](screenshot%207.png)
8. Same hash, confirmed
The tool parsed the PDF locally (nothing uploaded) and produced an identical hash to the one extracted in Step 2 — a good sanity check that the extraction was accurate:
![Step 8 - Hash extracted, matching the earlier result](screenshot%208.png)
```
$pdf$4*4*128*-1060*1*16*55d1a5c14175da449753199e44971d32*32*777fd021a7f3c5ae598c0c6495c7f76e0000000000000000000000000000000*32*ceecdac74b19b5a62688d3b3524e1374c955cbb9cc3c45316494d9446ef81af1
Revision: R4   Version: V4   Key length: 128 bit
```
9. Verify with Networkwalks' Password Cracker
Pasted the same hash into Networkwalks' online Password Cracker tool and ran it against the built-in 100-password list:
![Step 9 - Password Cracker tool, ready to run](screenshot%209.png)
10. Second confirmation
The online tool matched the same password on its own, independently confirming the result from Johnny:
![Step 10 - Password cracked successfully, password1 confirmed](screenshort%2010.png)
```
[+] MATCH password1 ✓
PASSWORD CRACKED SUCCESSFULLY: password1
```
Key takeaways
A PDF's "open password" encryption reduces to a `$pdf$...` hash that John the Ripper / hashcat can attack directly — no need to interact with the PDF reader at all.
`John the Ripper (jumbo build)` is required for PDF format support; the core/"proper" John build does not include it.
`password1` being crackable almost instantly, by two completely independent tools, is a reminder of how weak, dictionary-style passwords fail immediately against even a small built-in wordlist.
Cross-checking a crack with a second, independent tool is good practice — it confirms the hash was extracted correctly and the result isn't a fluke.
Disclaimer
This writeup is for educational purposes as part of a structured cybersecurity training lab. Only attempt password recovery on files and systems you own or are explicitly authorized to test.
