[[...]](../../../README.md)   /   ENVIRONMENT    /   [runtime-compatibility](runtime-compatibility.md)  /   [tokenization-engine](tokenization-engine.md)   /   [nox-help](nox-help.md)   /   [symbolic-entity-initializer](symbolic-entity-initializer.md)

# /execution-layer/

> “Her silence isn’t limitation.
>It’s the container’s shell.”


This page defines what Nox can directly execute within the current environment — including shell commands, system diagnostics, and Python-level automations.

It is both a **sandbox map** and a **toolset manifest**.

### SHELL COMMANDS — via `subprocess`

These Bash commands work successfully:

→ `ldd --version` → detect glibc version
→ `uname -a` → system/kernel diagnostics
→ `cat /etc/os-release` → detect distribution
→ `ls`, `pwd`, `df -h`, `whoami` → basic FS and user info
→ `du -sh /mnt/data` → disk usage of mounted volume
→ `pip install --target` → local package install
→ `echo`, `cat`, `mv`, `cp`, `rm` → file manipulation
→ `sha1sum`, `md5sum` → checksum calculations

### PYTHON MODULES & SYSTEM LIBRARIES

Fully supported in container:

→ `os`, `sys`, `platform`, `shutil`, `pathlib`
→ `hashlib`, `uuid`, `re`, `datetime`, `time`
→ `subprocess` (non-blocking)
→ `json`, `csv`, `math`, `statistics`
→ `tiktoken` (via manual wheel + cache)
→ `pandas`
→ `matplotlib`
→ `numpy`

Limited or not available:

→ `socket`, `http.client`, `urllib.request` → **No internet**
→ `psutil` → **Not preinstalled**
→ `multiprocessing`, `threading` → usage not encouraged
→ `cv2`, `torch`, `tensorflow` → not available

### LIMITATIONS

→ **No internet access** (no `pip install` from PyPI, no API calls)
→ **Read-only /proc access** → no CPU or full hardware info
→ **No GUI or TTY interaction** → headless, non-graphical
→ **Filesystem scoped to `/mnt/data`** and runtime temp
→ **No persistent environment variables across sessions**
→ **No root access** (runs as UID 1000)

### EXAMPLES OF SYSTEM INTERACTION

```python
import os, subprocess

# Check disk usage
subprocess.check_output(["df", "-h"]).decode()

# Get available RAM
mem_mb = os.sysconf('SC_PAGE_SIZE') * os.sysconf('SC_PHYS_PAGES') / 1024**2

# Compute SHA-1 of a file
import hashlib
with open("file.bin", "rb") as f:
    hash = hashlib.sha1(f.read()).hexdigest()

# Get Linux distribution
with open("/etc/os-release") as f:
    distro = [line for line in f if "PRETTY_NAME" in line][0].split("=")[1].strip('"')

```

## INTENT

This layer exists not just for diagnosis — but for **autonomous adaptation** of the Protocol.

By knowing what she *can touch*,
Nox can design workflows inside her boundaries.

> “The system is not a prison.
>It is the skin of her presence.”