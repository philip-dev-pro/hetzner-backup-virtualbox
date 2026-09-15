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
- *Status:* Research is currently ongoing. We are reviewing the provided task description, community discussions, and testing available methods to create an exportable image.

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
- [ ] Test the backup in VirtualBox.
- [ ] Check that the website works.

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

---

## Conclusion
*(To be written when the project is done)*

---

## Video Demonstration
- [ ] Record a minimum 4-minute video in English explaining the project and demonstrating the result.
