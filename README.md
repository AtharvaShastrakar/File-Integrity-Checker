# File Integrity Checker

A command-line tool that verifies the integrity of log files to detect tampering using **SHA-256** cryptographic hashing.

---

## 🎯 Requirements Fulfilled

1. **Accepts directory or single log file** — handles paths to individual files or recursively scans directories.
2. **Cryptographic hashing (SHA-256)** — generates secure SHA-256 digests of all log contents.
3. **Secure storage on first use** — hashes are saved in a protected database file with restricted `0600` permissions (owner read/write only).
4. **Comparison against baselines** — computes the current hash and matches against stored hashes.
5. **Clear discrepancy reporting** — reports `Status: Modified (Hash mismatch)` with stored vs current hashes.
6. **Manual re-initialization** — `update` command updates the stored baseline after legitimate changes.

---

## 🚀 Usage

### 1. Initialize Hashes (Stores baseline hashes)
```bash
./integrity-check init <path-to-file-or-dir>
```
*Example:*
```bash
./integrity-check init ./logs
# Output: Hashes stored successfully.
```

### 2. Check File Integrity
Supports both `check` and `-check` command formats:
```bash
./integrity-check check <path-to-file-or-dir>
# or
./integrity-check -check <path-to-file-or-dir>
```

- When the file has not been modified:
```
Status: Unmodified
```
- When the file has been tampered with:
```
Status: Modified (Hash mismatch)
  Stored:  9666634e01f5f9efd582fc337239259b4a267e0ecf900e9cb4c6344c04d4d1bf
  Current: 349d7c2bec21cf142fafb542428a9d1fe80ae0cb707ac15735f92df305f3373d
```
*(Returns exit code `1` when tampering is detected, making it ideal for CI/CD or automated alerting scripts)*

### 3. Update / Re-initialize Baseline
```bash
./integrity-check update <path-to-file-or-dir>
```
*Example:*
```bash
./integrity-check update ./logs/syslog
# Output: Hash updated successfully.
```

### 4. Check Overview Status
```bash
./integrity-check status
```
Displays an overview table of all monitored log files with their status (`OK`, `MODIFIED`, or `MISSING`).

---

## ⚙️ Quick Demo Run
Try this sample flow directly in your terminal:

```bash
# 1. Create a dummy log directory and file
mkdir -p demo_logs
echo "system starting" > demo_logs/syslog
echo "user login" > demo_logs/auth.log

# 2. Initialize
./integrity-check init demo_logs

# 3. Check clean file
./integrity-check -check demo_logs/auth.log

# 4. Tamper with file
echo "malicious unauthorized edit" >> demo_logs/syslog

# 5. Detect tampering
./integrity-check check demo_logs/syslog

# 6. Re-initialize / Update
./integrity-check update demo_logs/syslog
```
