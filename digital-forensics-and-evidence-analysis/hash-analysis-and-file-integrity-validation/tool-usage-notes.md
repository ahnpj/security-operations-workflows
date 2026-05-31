# Tool Usage Notes — Hash Analysis and File Integrity Validation

## Overview

This document summarizes the tools, commands, hashing methods, and evidence validation concepts used throughout the Hash Analysis and File Integrity Validation workflow.

The workflow focused on:

- generating hashes from strings,
- generating hashes from files,
- comparing MD5, SHA1, and SHA256 algorithms,
- validating file integrity,
- identifying files through hash values,
- reviewing Windows and Linux hashing methods,
- understanding hash lookup techniques.

Unlike encryption-focused workflows, this exercise focused on one-way hashing functions used to uniquely identify data and verify integrity.

---

## Primary Hashing Utilities — md5sum, sha1sum, and sha256sum

### Purpose

The primary tools used during this workflow were Linux hashing utilities:

```bash
md5sum
sha1sum
sha256sum
```

These utilities generate cryptographic hash values from files or supplied input.

A hash can be thought of as a digital fingerprint. If the input changes, even by a single character or byte, the resulting hash changes.

### How They Were Used

Hashes were generated from both text strings and files.

```bash
echo -n "My name is Peter Ahn!" | md5sum
```

```bash
echo -n "BTL1 rocks!" | sha256sum
```

```bash
md5sum tastypancakes.jpg
sha1sum tastypancakes.jpg
sha256sum tastypancakes.jpg
```

### Operational Relevance

Hashing utilities are among the most frequently used tools in digital forensics. Common use cases include evidence integrity validation, forensic image verification, malware identification, artifact correlation, and file comparison.

---

## Text Input Hashing Using echo

### Purpose

The `echo` command was used to provide text input directly to hashing utilities.

### How It Was Used

```bash
echo -n "My name is Peter Ahn!" | md5sum
```

The `-n` switch suppresses the newline character normally added to the end of the output.

### Common Mistakes

A common misunderstanding is assuming:

```bash
echo -n "hashthis.jpg" | md5sum
```

and

```bash
md5sum hashthis.jpg
```

produce the same result.

They do not. The first hashes the text string `hashthis.jpg`, while the second hashes the contents of the actual file.

---

## Pipe Operator ( | )

### Purpose

The pipe operator transfers output from one command directly into another command.

### Operational Relevance

Pipes allow investigators to chain commands together efficiently and automate common analysis workflows.

---

## Windows Hashing — Get-FileHash

### Purpose

`Get-FileHash` is a PowerShell cmdlet used to calculate file hashes on Windows systems.

### How It Was Used

```powershell
Get-FileHash -Algorithm SHA1 hashthis.jpg
```

### Operational Relevance

PowerShell hashing is commonly used during incident response, malware investigations, evidence collection, artifact validation, and integrity verification.

---

## Hash Comparison and Multi-Algorithm Validation

### Purpose

Multiple hashing algorithms were generated for the same file.

### Why Multiple Algorithms Exist

MD5, SHA1, and SHA256 all generate fingerprints of data but differ in output length, collision resistance, and modern security suitability.

---

## Public Hash Lookup Databases

### Purpose

Public hash databases maintain collections of previously generated hashes and their associated plaintext values.

### Important Clarification

Hash lookup services do not decrypt hashes. They compare submitted hashes against previously known values and return a match if one exists.

---

## Tool Strengths Observed

- Built into most Linux distributions
- Simple command syntax
- Fast execution
- Repeatable results
- Broad industry adoption
- Compatible with forensic workflows

---

## Tool Limitations Observed

The tools only generate hash values. They do not explain what the file is, whether it is malicious, or what the contents represent.

---

## Common Mistakes to Avoid

- Confusing hashing with encryption
- Assuming hash lookup websites perform decryption
- Confusing filename hashing with file-content hashing
- Assuming a matching hash automatically means malicious activity

---

## Suggested Screenshot Documentation

| Screenshot | Purpose |
|------------|----------|
| MD5 string hashing output | Demonstrates hashing text input |
| SHA256 string hashing output | Demonstrates algorithm comparison |
| PowerShell Get-FileHash example | Demonstrates Windows hashing |
| MD5 file hashing output | Demonstrates file fingerprint generation |
| SHA1 file hashing output | Demonstrates multi-algorithm comparison |
| SHA256 file hashing output | Demonstrates stronger hash generation |
| Hash lookup website result | Demonstrates hash identification workflow |

---

## Summary

This workflow demonstrated how common hashing utilities can be used to generate, compare, and validate cryptographic hash values for both strings and files. The exercises reinforced the role of hashing in digital forensics, incident response, evidence validation, and file identification.
