# 🚩 CTF Writeup: Trivial Flag Transfer Protocol

![Category](https://img.shields.io/badge/Category-Network%20Forensics%20%2F%20Stego-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-yellow)
![Tools](https://img.shields.io/badge/Tools-Wireshark%20%7C%20Steghide%20%7C%20CyberChef%20%7C%20ROT13-orange)

## 📌 Challenge Overview

| Property | Details |
| :--- | :--- |
| **Challenge Name** | Trivial Flag Transfer Protocol |
| **Platform** | picoCTF / HTB Academy |
| **Category** | Network Forensics & Steganography |
| **Artifacts Provided** | `tftp.pcapng` (Packet Capture) |
| **Target Protocol** | TFTP (Trivial File Transfer Protocol) — UDP Port 69 |

---

## 🎯 Executive Summary
The target packet capture contains unencrypted communications over the **Trivial File Transfer Protocol (TFTP)**. By extracting transferred files from the capture, deciphering ROT13-encoded messages, identifying a packaged steganography tool (`steghide`), and leveraging an exposed passphrase (`DUEDILIGENCE`), we extract the hidden flag from an embedded bitmap image.

---

## 🛠️ Tools Used
- **[Wireshark](https://www.wireshark.org/)**: PCAP inspection, protocol filtering, and object extraction.
- **ROT13 / [rot13.com](https://rot13.com/)**: Classical substitution cipher decoding.
- **[Steghide](https://steghide.sourceforge.net/)**: Steganographic data extraction from bitmap (`.bmp`) images.
- **Linux CLI (`dpkg-deb` / `ar`, `cat`)**: Debian package inspection and terminal forensics.

---

## 🔍 Step-by-Step Walkthrough

### 1. Packet Capture Analysis (Wireshark)
We begin by opening `tftp.pcapng` in Wireshark. Since the challenge title references TFTP, we apply the display filter:

```wireshark
tftp.type
```

![Wireshark TFTP Traffic Filter](<./evidence/Screenshot 2026-11-08 121025.png>)

#### Observations:
We observe cleartext communication between `10.10.10.11` and `10.10.10.12`:
- `Write Request`: `instructions.txt`
- `Read Requests`: `plan`, `program.deb`, `picture1.bmp`, `picture2.bmp`, `picture3.bmp`

Inspecting **Packet 1** confirms a TFTP Write Request (Opcode 2) transferring `instructions.txt` in octet mode:

![Wireshark Packet 1 Details](<./evidence/Screenshot 2026-11-08 121042.png>)

---

### 2. Exporting Captured Objects
Because TFTP transmits files in plaintext without encryption, we can extract the payloads directly using Wireshark:
1. Navigate to **File** ➔ **Export Objects** ➔ **TFTP...**
2. Click **Save All** and save the files to `~/Downloads`.

![Wireshark Export Objects Dialog](<./evidence/Screenshot 2026-11-08 121103.png>)

Viewing the destination folder confirms all 6 transferred files are intact:

![Extracted Files in File Manager](<./evidence/Screenshot 2026-11-08 121125.png>)

The extracted objects include:
- `instructions.txt` (113 bytes)
- `plan` (59 bytes)
- `program.deb` (138 kB)
- `picture1.bmp`, `picture2.bmp`, `picture3.bmp` (Bitmap images)

---

### 3. Deciphering the Instructions (ROT13)

Opening `instructions.txt` reveals an uppercase ciphertext string:

```text
GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA
```

Decrypting the message via ROT13 reveals:

![ROT13 Decoding Instructions](<./evidence/Screenshot 2026-11-08 121145.png>)

```text
TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN
```

> **Decoded Message:** *"TFTP DOESN'T ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAG TRANSFER. FIGURE OUT A WAY TO HIDE THE FLAG AND I WILL CHECK BACK FOR THE PLAN."*

---

### 4. Decoding the Plan (ROT13)

Next, inspecting the `plan` file reveals a second ciphertext string:

```text
VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYYVTRAPR.PURPXBHGGURCUBGBF
```

Decrypting with ROT13 yields:

![ROT13 Decoding Plan](<./evidence/Screenshot 2026-11-08 121209.png>)

```text
IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
```

> **Key Intel:**
> 1. **Tool hint:** *"I USED THE PROGRAM"* refers to `program.deb`.
> 2. **Passphrase:** `DUEDILIGENCE`
> 3. **Target carrier:** *"CHECK OUT THE PHOTOS"* points to the `.bmp` images.

---

### 5. Identifying the Tool (`program.deb`)

`program.deb` is a Debian package transferred across the network. Inspecting its package metadata confirms the application:

```bash
dpkg-deb -I program.deb
```
*or extracting the control archive:*
```bash
ar x program.deb && tar -tf control.tar.*
```

The package contains **`steghide`**, confirming that steganography was applied to conceal data inside one of the bitmap files.

---

### 6. Steganography Extraction & Flag Capture

Using `steghide` with the discovered passphrase (`DUEDILIGENCE`), we test the extracted images.

1. **Attempting `picture1.bmp`:**
   ```bash
   steghide extract -sf ./picture1.bmp -p DUEDILIGENCE
   ```
   *Output:* `steghide: could not extract any data with that passphrase!`

2. **Attempting `picture3.bmp`:**
   ```bash
   steghide extract -sf ./picture3.bmp -p DUEDILIGENCE
   ```
   *Output:* `wrote extracted data to "flag.txt".`

3. **Displaying the Flag:**
   ```bash
   cat flag.txt
   ```

![Terminal Extraction and Flag Capture](<./evidence/Screenshot 2026-11-08 121539.png>)

#### Flag Captured:
```text
academy{h1dd3n_1n_pLa1n_51GHT_18375919}
```

---

## 🛡️ Key Takeaways & Lessons Learned
1. **Unencrypted Legacy Protocols:** TFTP lacks confidentiality and authentication. Any network sniffer can reassemble and export transferred files without credential requirements.
2. **Obfuscation is Not Encryption:** ROT13 provides trivial Caesar substitution with zero security against statistical analysis or basic cryptanalysis.
3. **OpSec Failure:** Transmitting both the steganography payload, the installation package (`steghide`), and the passphrase (`DUEDILIGENCE`) across the same unencrypted network capture completely defeats the purpose of data concealment.