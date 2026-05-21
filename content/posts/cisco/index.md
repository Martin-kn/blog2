---
title: Cisco Switch Firmware Update — Illegal Copy & Oversize Fix
date: 2021-03-10
categories:
  - server
tags: ["Cisco","firmware","networking","switch","TFTP"]
---


A problem I encountered when updating the firmware on a second-hand switch. Errors like 'illegal copy' or 'oversize' can appear during the process — in my case the model is the SF300-24, but these errors are not limited to this model.


### Illegal copy

- **"Illegal software format copy"** error when uploading firmware → the switch rejects the file because the firmware version is too far ahead of the currently installed one.

![Cisco1!](/images/cisco/cisco1.png)

`Solution`: install an intermediate firmware version first, then upgrade step by step until reaching the target version. Firmware files can be downloaded from the official Cisco Software Downloads page (requires a Cisco account).



### Oversize

- **"Oversize"** error when uploading firmware → the file is too large for the current bootloader to handle.


![Cisco2!](/images/cisco/cisco2.png)

`Solution:` download the firmware variant that includes the bootloader (available on Cisco's download page — look for the version that comes as a .zip containing both the firmware and the bootloader). 
The bootloader must be installed via `TFTP` — a direct web upload will not work for this step.

To set up a TFTP server:
- Windows: use Tftpd64
- Linux: install tftpd-hpa (`apt install tftpd-hpa`)

Once the TFTP server is running and the file is in the correct directory, initiate the transfer from the switch's web interface or CLI pointing to your TFTP server's IP.

