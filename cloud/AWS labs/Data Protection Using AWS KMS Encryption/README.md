# Data Protection Using AWS KMS Encryption 🔐☁️

## 📌 Overview

This lab demonstrates how encryption protects sensitive data by converting readable plaintext into unreadable ciphertext and restoring it through decryption.

The exercise focuses on practical cryptography implementation using AWS Key Management Service (AWS KMS) and the AWS Encryption CLI. A symmetric encryption key was created and used to encrypt and decrypt files hosted on an Amazon EC2 file server.

The lab simulates a common enterprise security workflow where sensitive information must be protected both at rest and during processing.

---

# 🏗️ Environment Overview

## Infrastructure Components

| Component                           | Purpose                      |
| ----------------------------------- | ---------------------------- |
| AWS KMS                             | Key management service       |
| Symmetric Encryption Key            | Encrypt and decrypt data     |
| Amazon EC2 File Server              | Encryption workstation       |
| AWS Systems Manager Session Manager | Secure instance access       |
| AWS Encryption CLI                  | Command-line encryption tool |
| Plaintext Files                     | Sensitive data simulation    |

## Encryption Architecture

## Data Flow

```text
Plaintext File
      |
      v
AWS Encryption CLI
      |
      v
AWS KMS Key
      |
      v
Encrypted Ciphertext
      |
      v
AWS Encryption CLI
      |
      v
Original Plaintext
```

---

# 🔑 Task 1 — Create an AWS KMS Encryption Key

## Objective

Create a symmetric encryption key that will be used to encrypt and decrypt sensitive data.

## Activities

* Opened AWS Key Management Service (KMS).
* Created a new Symmetric encryption key.
* Configured key details:

  * Alias: MyKMSKey
  * Description: Key used to encrypt and decrypt data files
* Assigned key administration permissions.
* Assigned key usage permissions.
* Recorded the generated Key ARN for future encryption operations.

## Key Configuration

| Setting  | Value             |
| -------- | ----------------- |
| Key Type | Symmetric         |
| Alias    | MyKMSKey          |
| Usage    | Encrypt / Decrypt |
| Service  | AWS KMS           |

## Screenshots

### Create KMS Key

<div align="center">
<img src="Images/task1-create-kms-key.png" alt="Create KMS Key" width="700">
</div>

### Key Configuration

<div align="center">
<img src="Images/task1-key-configuration.png" alt="Key Configuration" width="700">
</div>

### Generated Key ARN

<div align="center">
<img src="Images/task1-key-arn.png" alt="Generated Key ARN" width="700">
</div>

---

# 💻 Task 2 — Configure the File Server Environment

## Objective

Prepare the EC2 File Server for encryption operations.

## Activities

* Connected to the File Server instance using AWS Systems Manager Session Manager.
* Configured AWS credentials.
* Imported temporary AWS access credentials.
* Installed AWS Encryption CLI.
* Configured environment variables and execution path.

## Commands Executed

### Configure AWS Credentials

```bash
cd ~
aws configure
```

### Verify Credentials

```bash
cat ~/.aws/credentials
```

### Install AWS Encryption CLI

```bash
pip3 install aws-encryption-sdk-cli
```

### Configure Execution Path

```bash
export PATH=$PATH:/home/ssm-user/.local/bin
```

## Screenshots

### Session Manager Connection

<div align="center">
<img src="Images/task2-session-manager.png" alt="Session Manager Connection" width="700">
</div>

### AWS Credentials Configuration

<div align="center">
<img src="Images/task2-aws-credentials.png" alt="AWS Credentials Configuration" width="700">
</div>

### AWS Encryption CLI Installation

<div align="center">
<img src="Images/task2-encryption-cli-install.png" alt="AWS Encryption CLI Installation" width="700">
</div>

---

# 🔒 Task 3 — Create Sensitive Data Files

## Objective

Generate plaintext files that will later be encrypted.

## Activities

Created multiple test files representing sensitive information.

## Commands Executed

```bash
touch secret1.txt secret2.txt secret3.txt

echo 'TOP SECRET 1!!!' > secret1.txt
```

### Verify Plaintext Content

```bash
cat secret1.txt
```

Expected output:

```text
TOP SECRET 1!!!
```

## Screenshots

### Plaintext File Creation

<div align="center">
<img src="Images/task3-plaintext-creation.png" alt="Plaintext File Creation" width="700">
</div>

---


# 🔐 Task 4 — Encrypt Sensitive Data

## Objective

Encrypt plaintext files using AWS KMS and AWS Encryption CLI.

## Activities

*   Created an output directory for encrypted files.
*   Stored the KMS Key ARN in a variable.
*   Executed encryption operations using AWS Encryption CLI.
*   Generated encrypted ciphertext output.

## Commands Executed

### Create Output Directory
```bash
mkdir output
```

### Store Key ARN

### Encrypt File
```bash
aws-encryption-cli --encrypt \
  --input ~/secret1.txt \
  --wrapping-keys key=$keyArn \
  --metadata-output ~/metadata.json \
  --encryption-context purpose=test \
  --commitment-policy require-encrypt-require-decrypt \
  --output ~/output
```

### Verify Success
```bash
ls -l ~/output
```
Expected output:
```text
secret1.txt.encrypted
```

## Screenshots

### Encryption Command

<div align="center">
<img src="Images/task4-encryption-command.png" alt="Encryption Command" width="700">
</div>

### Encrypted File Output

<div align="center">
<img src="Images/task4-encrypted-output.png" alt="Encrypted File Output" width="700">
</div>

---

# 🔓 Task 5 — Decrypt Ciphertext

## Objective

Restore encrypted ciphertext back into readable plaintext.

## Activities

*   Executed decryption operations using AWS Encryption CLI.
*   Verified integrity of decrypted data.
*   Confirmed successful recovery of original file contents.

## Commands Executed

```bash
aws-encryption-cli --decrypt \
  --input ~/output/secret1.txt.encrypted \
  --wrapping-keys key=$keyArn \
  --commitment-policy require-encrypt-require-decrypt \
  --encryption-context purpose=test \
  --metadata-output /tmp/decrypt-metadata.json \
  --max-encrypted-data-keys 1 \
  --buffer \
  --output ~/
```

### Verify Decrypted File
```bash
ls ~/
```

### View Recovered Plaintext
```bash
cat ~/secret1.txt.encrypted.decrypted
```
Expected output:
```text
TOP SECRET 1!!!
```

## Screenshots

### Decryption Command

<div align="center">
<img src="Images/task5-decryption-command.png" alt="Decryption Command" width="700">
</div>

### Decrypted File

<div align="center">
<img src="Images/task5-decrypted-file.png" alt="Decrypted File" width="700">
</div>

### Restored Plaintext

<div align="center">
<img src="Images/task5-restored-plaintext.png" alt="Restored Plaintext" width="700">
</div>

---

Characteristics Symmetric encryption:

* Same key used for encryption and decryption.
* Fast and efficient.
* Commonly used for data-at-rest protection.
* Suitable for large-scale file encryption.

## AWS KMS Benefits

AWS Key Management Service provides:

* Centralized key management.
* Hardware Security Module (HSM) protection.
* Fine-grained access control.
* Auditability through AWS logging services.
* Secure integration with AWS services.

## Encryption Context

The encryption process included:

```text
purpose=test
```

Encryption contexts add additional authenticated metadata and help strengthen security controls around encrypted data.

---

# 🧠 Skills Gained

* AWS Key Management Service (KMS)
* Symmetric Encryption
* Cryptographic Key Management
* AWS Encryption CLI
* Data Protection Controls
* Encryption & Decryption Operations
* Secure File Handling
* AWS Systems Manager Session Manager
* Linux Command-Line Administration
* Cloud Security Fundamentals

---

# ✅ Conclusion

This lab demonstrated the practical implementation of cryptographic controls using AWS KMS and the AWS Encryption CLI.

By creating a symmetric encryption key, encrypting sensitive plaintext files, and successfully restoring them through decryption, the exercise reinforced fundamental principles of confidentiality, key management, and data protection.

The lab highlights how modern cloud platforms provide secure and scalable encryption services that organizations can use to protect sensitive information and support regulatory compliance requirements.
