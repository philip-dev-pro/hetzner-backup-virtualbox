# Project: Hetzner Cloud Server Backup to Local VirtualBox

## Team Members
- Philip Sjoholm
- Raul Velasquez

## Project Overview
The task is to find a way to create a downloadable backup file of a running Hetzner Cloud server (`wordpress.multinomial.se`, running Ubuntu 24.04 with WordPress) and restore it locally inside VirtualBox on a laptop.

---

## Phase 1: Research Phase (Feasibility Study) - WORK IN PROGRESS

### 1. Problem Statement
Hetzner Cloud does not allow users to download server backups as files; backups can only be restored inside Hetzner. We need to find an independent solution to extract the server as a file and run it outside of Hetzner.

### 2. Available Solutions & Research
We evaluated three primary methods for extracting the server image:

* Hetzner Native Snapshots: While fast and easy to create, they are proprietary and cannot be exported or downloaded as files, making them unsuitable for our goal of provider independence.

* Rsync (File-level migration): This would involve copying only the files and databases. While efficient in terms of size, it is complex to set up a new bootable OS and match all configurations manually in a local environment.

* DD (Disk Dump) via SSH: We decided on this method because it creates a bit-by-bit copy of the entire storage block. By piping the output through gzip over an SSH connection, we could stream a complete, bootable image directly to our local machine without needing extra storage on the cloud server.

---

## Phase 2: Implementation & Progress

### Tasks:
- [x] Formed team and agreed on work split.
- [x] Created GitHub repository.
- [x] Added team members as repository collaborators.
- [x] Requested access to server SSH keys on Google Drive.
- [x] Receive SSH key access.
- [x] Connect to `wordpress.multinomial.se` via SSH.
- [x] Inspect server setup.
- [x] Create and download the backup file.
- [x] Restore and boot the backup inside VirtualBox.
- [x] Verify WordPress works locally.

---

## Phase 3: Testing & Finalization
- [x] Test the backup in VirtualBox.
- [x] Check that the website works.
Once the backup file was downloaded, we finalized the migration with these steps:

1.Decompression & Conversion: We used gunzip to extract the raw image and then utilized VBoxManage to convert it into a .vdi format, which is native to VirtualBox.
2.VM Configuration: A new Virtual Machine was created with 4GB RAM and 2 CPUs. We specifically enabled EFI (Extensible Firmware Interface) in the system settings to match the Hetzner Cloud boot requirements.
3.Network Hardware Abstraction: Because the virtualized hardware differs from the cloud environment, we updated the Netplan configuration inside Ubuntu to use DHCP. This allowed the VM to receive a local IP address from our router.
4.Verification: We confirmed that the apache2 (web server) and mysql (database) services started automatically upon boot.
---

## Phase 4: Problems & Solutions

### Problem 1: Large Raw Disk Size (41 GB)
* **Problem:** The cloud server's hard drive was 41 GB, which would take too long to download and waste bandwidth, even though only ~4.6 GB of actual data was used.
* **Solution:** We piped the disk image through `gzip -1` in real-time over SSH. This compressed the 41 GB disk down to only 3.4 GB, completing the download in just 4 minutes.

### Problem 2: Mouse and Keyboard Captured in VirtualBox
* **Problem:** When clicking inside the VirtualBox console, the mouse and keyboard got trapped because the laptop lacked a dedicated "Right Ctrl" key, and the Windows key was locked.
* **Solution:** Used `Ctrl + Alt + Delete` to break the capture and return focus to Windows, then adjusted the input settings in VirtualBox.

### Problem 3: No Root Password for Console Login
* **Problem:** Hetzner deployed the server using only SSH keys, meaning there was no known local root password to log in via the VirtualBox console screen.
* **Solution:** Booted the VM into GRUB Recovery Mode, remounted the filesystem as read-write (`mount -o remount,rw /`), and set a local password using `passwd root`.

### Problem 4: WordPress Redirecting to the Live Domain
* **Problem:** When navigating to the local VM's IP address (`192.168.1.99`), WordPress automatically redirected the browser to `wordpress.multinomial.se` (which pointed to Hetzner's live IP on the internet).
* **Solution:** Updated the local Windows `hosts` file (`C:\Windows\System32\drivers\etc\hosts`) to map `wordpress.multinomial.se` directly to the local VM IP (`192.168.1.99`), ensuring all traffic stayed 100% local.

### Problem 5: Virtual Machine Freezing and Long Boot Delays
* **Problem:** The restored virtual machine took several minutes to boot and frequently appeared completely stuck during startup. This happened because `cloud-init` was continuously searching and waiting for network responses from Hetzner's cloud metadata services, which do not exist outside of Hetzner.
* **Solution:** We disabled cloud-init by running `touch /etc/cloud/cloud-init.disabled` inside the server. This completely removed the timeout loops, prevented the machine from hanging, and reduced boot time down to just a few seconds.

---

## Conclusion
*(To be written when the project is done)*

---

## Video Demonstration
- [ ] Record a minimum 4-minute video in English explaining the project and demonstrating the result.
