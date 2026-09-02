---
title: Clonezilla
author: Ali Ahmed
tags:
  - clonezilla
  - disk cloning
  - system backup
---

## Table of contents:

- [1 What is Clonezilla](#1-what-is-clonezilla)<br>
  - [1.1 Why use Clonezilla](#11-why-use-clonezilla)
  - [1.2 Notes](#12-notes)
- [2 Installing clonezilla live](#2-installing-clonezilla-live)
  - [2.1 Downloading and verifying the ISO file](#21-downloading-and-verifying-the-iso-file)
  - [2.2 Making a bootable USB](#22-making-a-bootable-usb)
- [3 How to do a full-system backup using Clonezilla](#3-how-to-do-a-full-system-backup-using-clonezilla)
 
## 1 What is Clonezilla
Clonezilla is a user-freindly partition and disk imageing/cloning tool. It allows you to clone your entire system to another disk.<br><br>

### 1.1 Why use Clonezilla

When you use Clonezilla to clone your disk (let's call it disk A) to another disk (let's call it disk B), B becomes an exact copy of A (same partitions and partition table, same UUIDs, same everything) which makes it useful for making backups or even using disk B on another machine with zero setup required (so effectively you can run your EXACT system on multiple machines)<br><br>

This is different from disk copying which would require you to setup disk B by creating partitions and a partition table before copying the system which won't even boot before you modify it to use the new UUIDs of disk B.<br><br>

### 1.2 Notes

- 1 Clonezilla uses partclone by default. However, for the filesystems that aren't supported by partclone Clonezilla uses dd.<br>
- 2 BTRFS cloning is buggy in partclone and you will probably get erros. If that happens try forcing Clonezilla to use dd.
<br><br>

## 2 Installing Clonezilla live

As the name suggests, Clonezilla **live** should be installed on a bootable USB to **live**-boot from.<br><br>
> Note: at the time I am writing this, the latest version of clonezilla and the version I am using for this guide is 3.3.2-31
<br>

### 2.1 Downloading and verifying the ISO file

First you download the ISO file from [here](https://sourceforge.net/projects/clonezilla/).<br>
Also make sure to download the checksums and checksums.gpg files next to "stable - 3.3.2-31" [here](https://clonezilla.org/downloads.php) in order to verify your downloaded ISO.<br>
It is preferable to download the three files above in the same folder.<br><br>

Next run the following command as root:<br>
```text
# gpg --keyserver hkp://keys.openpgp.org --recv-key 667857D045599AFD
```
Then run the following command as root:<br>
```text
# gpg --verify CHECKSUMS.TXT.gpg CHECKSUMS.TXT
```
It should output:<br>
```text
gpg: Signature made Fri 03 Jul 2020 07:59:54 PM CST
gpg:                using RSA key 54C0821A48715DAFD61BFCAF667857D045599AFD
gpg: Good signature from ...
```
If the output includes:
```text
Bad signature
```
That means that the checksum file is corrupted or has been tampered with.<br>
Delete the three files and redo the steps in this section.<br><br>

Lastly, run the following command as root:
```text
# b2sum -c CHECKSUMS.TXT 2>&1 | grep OK
```
It should output:
```text
clonezilla-live-3.3.2-31-amd64.iso: OK
```
If it doesn't output anything that means that the ISO file is corrupted or has been tampered with.<br>
Delete the three files and redo the steps in this section.<br><br>

### 2.2 Making a bootable USB
You can use Etcher to flash the ISO file to the USB or better yet, you can use Ventoy, which I covered in another article in this wiki.

## 3 How to do a full-system backup using Clonezilla

Shutdown your computer and boot Clonezilla from the bootable USB. First thing you'll see is this:<br>
![Clonezilla boot menu](imgs/clonezilla/bootmenu.png)
Select the first option: Clonezilla live (VGA 800x600)<br>
You will see the Debian booting process (fun fact: Clonezilla is based on Debian).<br><br>

Then you will be prompted to choose a language:<br>
![language select](imgs/clonezilla/lang.png)<br>
Pick any language you like. this guide will use English.<br><br>

After that you will have the option to change or keep the keyboard layout:<br>
![keyboard layout select](imgs/clonezilla/keymap.png)<br>
This guide will use the default layout (US keyboard)<br><br>

Then you can choose whether to start clonezilla or enter the command line:
![start clonezilla](imgs/clonezilla/start-clonezilla.png)<br>
Select Start_Clonezilla.<br>
<br>

Upon starting Clonezilla, you will be prompted to select the type of operation you want to perform.
![operations](imgs/clonezilla/device-device-clone.png)<br>
For this guide we will select device-device.<br><br>

When you see this screen:<br>
![disk to disk clone](imgs/clonezilla/disk-to-local-disk-clone.png)<br>
Select disk_to_local_disk_clone unless you want to just clone a single partition.<br><br>

You will now see this screen prompting you to choose which mode to run:<br>
![mode select](imgs/clonezilla/beginner-expert-mode.png)<br>
Select Beginner (unless you want to set certain advanced parameters which I won't cover in this article).<br><br>

When you see this screen:<br>
![source disk](imgs/clonezilla/source-disk.png)<br>
Press Space on the disk you want to clone then press Enter.<br><br>

Then select the target disk and press enter:
![target disk](imgs/clonezilla/target-disk.png)<br><br>

Choose whether to check the source file system:<br>
![source filesystem needs to be checked?](imgs/clonezilla/fsck-src-fs.png)<br>
Usually you want to skip checking. If that is the case select sfsck.<br><br>

Choose whether to scale to scale the partition table proportionally with the destination disk's storage:<br>
![partition table settings](imgs/clonezilla/fdisk-k.png)<br>
Usually you want to select k1 but personally I like to select k0 to have empty unpartitioned space on my target disk because my target disk has a large amount of storage and I don't want to waste it all.<br><br>

Select what you want the system to do after the cloning is done:<br>
![postrun](imgs/clonezilla/postrun.png)<br><br>

Follow the on-screen instructions and warnings.<br>
It will take some time to finish cloning and when it is done, you can use the cloned disk as a backup or swap it into another machine and boot straight into your system with zero setup required.
