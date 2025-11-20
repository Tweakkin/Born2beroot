# Born2beroot

**Born2beroot** is a system administration project that involves setting up a virtual machine, configuring a secure operating system (Debian), implementing strict security policies, and automating system monitoring.

This document explains the core concepts and configurations used in this project.

---

## 📚 Table of Contents
- [1. Virtualization](#1-virtualization)
- [2. Operating System (Debian)](#2-operating-system-debian)
- [3. Partitioning & LVM](#3-partitioning--lvm)
- [4. User & Password Management](#4-user--password-management)
- [5. SSH & Security](#5-ssh--security)
- [6. Firewall (UFW)](#6-firewall-ufw)
- [7. Monitoring Script](#7-monitoring-script)

---

## 1. Virtualization
Virtualization allows you to run multiple operating systems on a single physical machine.
* **Host OS:** The physical machine (e.g., the iMac/PC).
* **Guest OS:** The virtual machine (Debian).
* **Hypervisor:** Software that creates and runs VMs.
    * *Type 1 (Bare Metal):* Installed directly on hardware (e.g., Xen, ESXi).
    * *Type 2 (Hosted):* Installed on an OS (e.g., VirtualBox, VMWare). We used VirtualBox.

---

## 2. Operating System (Debian)
We chose **Debian** (specifically Debian Stable) for this server.
* **Debian vs. CentOS:** Debian is known for its stability and package management (`apt`). CentOS was the community version of Red Hat Enterprise Linux but has changed direction; Debian remains a pure community-driven OS.
* **Apt vs. Aptitude:**
    * `apt`: Lower-level package manager. Fast and efficient.
    * `aptitude`: Higher-level package manager with a text-based UI (ncurses). It handles dependencies smarter than apt in complex scenarios.
* **AppArmor:** A security module (Mandatory Access Control) that restricts programs' capabilities with per-program profiles.

---

## 3. Partitioning & LVM
We used **LVM (Logical Volume Manager)** for partitioning.
* **Structure:**
    1.  **PV (Physical Volume):** The raw hard drive or partition.
    2.  **VG (Volume Group):** A pool of storage created from PVs.
    3.  **LV (Logical Volume):** The virtual partitions created from the VG (e.g., `/root`, `/home`, `/var`).
* **Why LVM?** It allows dynamic resizing of partitions without reformatting the disk. You can add more space to a specific partition on the fly.
* **Encryption:** The partitions are encrypted, requiring a passphrase at boot to mount the filesystem.

---

## 4. User & Password Management
Security is enforced through strict user and password policies using `sudo` and `libpam-pwquality`.

### Sudo (SuperUser DO)
Sudo allows a permitted user to execute a command as the superuser (root).
* **Configuration:** stored in `/etc/sudoers` (edited via `visudo`).
* **Rules implemented:**
    * Limit authentication retries (3 attempts).
    * Custom error messages.
    * Log all sudo actions to `/var/log/sudo/`.
    * TTY mode enabled for security.

### Password Policy
Configured in `/etc/login.defs` and `/etc/pam.d/common-password`.
* **Expiration:** Passwords expire every 30 days.
* **Complexity:**
    * Minimum 10 characters.
    * Must contain uppercase, lowercase, and numbers.
    * Cannot contain the username.
    * Must differ from the previous password by at least 7 characters.

---

## 5. SSH & Security
**SSH (Secure Shell)** is a protocol for operating network services securely over an unsecured network.
* **Port 4242:** We changed the default SSH port from 22 to 4242 to avoid automated scripts scanning for default ports (Security through Obscurity).
* **Root Login:** Disabled. You cannot SSH directly as root; you must connect as a user and `su` or `sudo` up.

---

## 6. Firewall (UFW)
**UFW (Uncomplicated Firewall)** is used to manage `iptables` (netfilter).
* **Status:** Active.
* **Rules:** All incoming connections are blocked by default, except for port **4242**.

---

## 7. Monitoring Script
A bash script (`monitoring.sh`) runs every 10 minutes via **Cron** to display system architecture and usage information to all terminals.

**Commands used in the script:**
* `uname -a`: Architecture and kernel version.
* `nproc`: Number of physical/virtual processors.
* `free -m`: RAM usage.
* `df -h`: Disk usage.
* `top` / `mpstat`: CPU load.
* `who -b`: Last boot time.
* `ss` / `netstat`: Active connections.
* `wall`: Broadcasts the message to all users.

---
*Project created by Tweakkin.*