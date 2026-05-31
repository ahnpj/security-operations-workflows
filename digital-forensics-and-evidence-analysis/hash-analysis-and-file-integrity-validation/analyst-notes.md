# Analyst Notes — Hash Analysis and File Integrity Validation

This file captures informal observations, conceptual explanations, confusion points, and operational takeaways recorded during execution of the hash analysis and file integrity validation workflow.

These notes preserve context that did not fully belong inside the formal workflow execution write-up but was useful for understanding why hashing matters, how forensic investigators use hashes, and how integrity validation supports digital forensic investigations.

> **Workflow vs Examination vs Conceptual Review (Terminology Used Here)**  
> - **Workflows** refer to structured forensic tasks such as hash generation, evidence validation, artifact comparison, and hash lookup activities.  
> - **Examinations** refer to the hands-on use of hashing utilities against text strings and files.  
> - **Conceptual Reviews** refer to the interpretation of hashing behavior, integrity validation, algorithm differences, and forensic relevance.

---

### Observations During Execution

One of the biggest realizations during this workflow was that a hash is essentially a fingerprint for data.

Before this exercise, it was easy to think of hashes as just long strings of random characters. However, generating hashes from both text strings and files helped reinforce that hashes are actually identifiers. They provide a repeatable method of representing data without needing to view the data itself.

The workflow also demonstrated that hashing is used far more frequently than many people realize. Hashes appear in digital forensics, malware analysis, incident response, threat intelligence, software verification, and evidence handling.

---

### Understanding Hashes as Digital Fingerprints

The most useful way to think about a hash is as a fingerprint for data.

A file may contain thousands or millions of bytes. Rather than comparing every byte manually, investigators can generate a hash and compare the resulting value.

If two files generate the same hash using the same algorithm, there is a high degree of confidence that the files are identical.

If the hash changes, something about the file changed.

---

### Why the echo -n Command Was Important

One confusing point during the workflow was understanding why the command used:

```bash
echo -n "BTL1 rocks!" | md5sum
```

instead of:

```bash
echo "BTL1 rocks!" | md5sum
```

Without `-n`, the `echo` command adds a newline character to the end of the text. Although that newline is not visible, it becomes part of the input being hashed.

This means the resulting hash changes completely.

---

### File Hashing vs Filename Hashing

Initially it was tempting to assume that:

```bash
echo -n "hashthis.jpg" | md5sum
```

and

```bash
md5sum hashthis.jpg
```

would produce the same result.

They do not.

The first command hashes the literal text string `hashthis.jpg`.

The second command hashes the actual contents of the file.

---

### Understanding MD5, SHA1, and SHA256

Another useful takeaway was understanding that MD5, SHA1, and SHA256 all perform the same general task.

They generate fingerprints of data.

The primary differences involve:

- output length,
- collision resistance,
- modern security suitability.

---

### Hashing Is Not Encryption

One of the most important concepts reinforced during this workflow was that hashing and encryption are fundamentally different technologies.

Encryption is designed to be reversible when the proper key is available.

Hashing is designed to be one-way.

The purpose of a hash is not to hide information and later recover it. The purpose is to generate a repeatable fingerprint for data.

---

### Understanding Hash Lookup Databases

The final exercise required identifying the plaintext value associated with a known MD5 hash.

At first glance, it appeared as though the website had decrypted the hash.

In reality, the site simply compared the submitted hash against a database of previously known values.

---

### Why Hashes Matter During Digital Forensics

Hashes allow investigators to:

- verify evidence integrity,
- identify known files,
- compare artifacts,
- validate forensic acquisitions,
- support chain-of-custody procedures.

---

### Connection to Previous Workflows

This workflow connects naturally to both previous digital forensic workflows.

The file system identification workflow focused on understanding how storage devices organize information through file systems such as NTFS, FAT32, and EXT3.

The metadata and file carving workflow focused on recovering and analyzing artifacts.

This hashing workflow builds upon both.

---

### Challenges and Confusion Encountered

The most confusing part of the workflow was not the commands themselves.

The commands were relatively straightforward.

The more confusing aspect was understanding what exactly was being hashed.

---

### Ideas for Expansion or Future Work

Future versions of this workflow could be expanded by:

- comparing MD5, SHA1, and SHA256 output lengths directly,
- generating hashes before and after modifying a file,
- validating forensic images using hashes,
- comparing PowerShell and Linux hash outputs,
- investigating VirusTotal hash lookups,
- exploring SHA512.

---

### Summary

This workflow reinforced that cryptographic hashes are far more than random strings of characters.

Hashes serve as fingerprints for data and provide investigators with a practical method for identifying files, validating evidence integrity, comparing artifacts, and supporting forensic investigations.
