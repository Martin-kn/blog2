---
title: Resize Kali Linux VM in Virtualbox or VMware
summary: "Step-by-step guide to expanding a Kali Linux VM disk partition in VirtualBox and VMware using GParted."
date: 2025-01-02
categories:
  - linux
tags: ["GParted","disk","virtualbox","vmware","kali"]
---

`Note:` This guide assumes a standard partition layout (root + swap). If your VM uses LVM, the process is different.

In this guide I'll explain how to expand the root partition of a Kali Linux VM (applies to any other Linux distro). The disk expansion steps vary depending on the hypervisor — VirtualBox or VMware — but the partition resizing process is the same for both.

## Virtualbox

In the case of VirtualBox, open the **Virtualbox Media Manager**:

`File -> Tools -> VirtualBox Media Manager` or use `Ctrl+D`

Select the VM and add the desired disk space. 
If the bottom section appears greyed out, refresh or shut down the VM first.

![image!](/images/kali-vm/1-1.png)

`In this example only 1GB was added — adjust according to your needs.`

## VMware 

If using **VMware**, go to your VM and select:

<!-- En virtual machine settings --> 

`Edit virtual machine settings → Virtual Machine Settings 
→ Hard Disk → Expand`

![image!](/images/kali-vm/vmware.png)

Select the amount of space to add and click "Expand".

![image!](/images/kali-vm/vmware2.png)


## Expanding the Root Partition

Once disk space has been added in the hypervisor, boot into Kali and open GParted (pre-installed in Kali Linux).

Here we can see the added space as "Unallocated", shown on the far right of the disk layout (dotted block).

![image!](/images/kali-vm/1b.png)

The unallocated block needs to be repositioned next to /dev/sda1 before we can expand the root partition. The steps below walk through that process.


Follow the steps shown below:

`NOTE:` Changes in GParted are not applied immediately. You can revert them if needed. To apply changes permanently, click the green checkmark (Apply All Operations)`

1. Expand /dev/sda2 to absorb the unallocated space

Right-click on /dev/sda2 → Resize/Move

![image!](/images/kali-vm/2.png)

Seleccionar la flecha negra y arrastrarla hacia la derecha

![image!](/images/kali-vm/3.png)

Result after resizing:

![image!](/images/kali-vm/4.png)

Click [Resize/Move] to confirm.

The unallocated block is now part of `/dev/sda2`:

![image!](/images/kali-vm/5.png)

2. Select Swapoff on the swap partition to allow resizing it.

![image!](/images/kali-vm/6.png)

Once done, the resize/move option becomes available — click it.

![image!](/images/kali-vm/7.png)

`Important:`  In this step do NOT increase the partition size. The goal is to shift the swap block to the right, freeing up unallocated space adjacent to the root partition.

Click and drag the white area of the block to move it to the right:

![image!](/images/kali-vm/8.png)  

3. Below is a short video showing how to move the block in 
/dev/sda2 to free up space for the root partition:

<video controls src="https://github.com/Martin-kn/blog2/raw/refs/heads/main/assets/images/kali3.mp4" ></video>


4. Now go to the root partition (/dev/sda1)

Right-click → Resize/Move

![image!](/images/kali-vm/10.png)

Drag the black arrow to the end of the block to expand the partition.

![image!](/images/kali-vm/11.png)


5. Apply all changes permanently:

![image!](/images/kali-vm/12.png)

Note: This operation modifies partition layout. Ensure you have a backup or snapshot before proceeding.

![image!](/images/kali-vm/13.png)

6. Finally, re-enable the swap partition:

![image!](/images/kali-vm/14.png)

Done — the root partition has been successfully expanded.


