---
title: "Cheatsheet for multibooting Windows and Linux on a Mac"
---

Installing on an external USB / IEEE 1394 drive, making a bootable Windows USB stick without using any third-party software, `blInitializeLibrary` error...

#### How to make a bootable Windows installer USB stick on a Mac

Create an MBR disk with a single FAT partition. Mount Windows ISO downloaded from Microsoft. Copy all files from ISO to USB. Then copy Mac drivers downloaded from Apple website (or via Bootcamp menu) on top, including `AutoUnattend.xml`.

Yes, that's it, just copy files, with UEFI nothing else is needed.

One should use MBR and **NOT GPT** because Windows installer refuses to proceed when it finds more than one disk in the system that has an EFI partition (which is implicitly created inside every GPT disk).

If Apple's Disk Utility refuses to let you choose MBR as a partitioning scheme (happens...), you can use command line `diskutil list` to find disk number that you want to format and then `sudo fdisk  -i -a dos /dev/rdisk_XXX_`. (Disclamer: it destroys all of your data. Make sure to get the number right.)

#### How to clone Linux (ext2, ext4) installation to a bigger or smaller partition

1. Copy files (needs rsync 3.x)

        sudo rsync -axHAXWS --info=progress2 --numeric-ids / /destination_mount_point/

2. Edit `fstab`

    On the original system run this to find the GUID (aka UUID) of the new volume:

        lsblk -f 

    Update `fstab` on the target volume to reflect the new GUID e.g.
    
        vi /destination_mount_point/etc/fstab
        
    and change the GUID for `/`

#### Booting Mac with an external monitor

Sometimes when booting with an external monitor and the lid closed your Mac may fail to display any picture. Connecting/disconnecting (or turning the monitor off and on) may help to fix that without opening the lid.

#### Installing Windows on an external drive

One can force install Windows on pretty much anything bootable (external drives, USB stick, SD card, etc) by skipping Windows installer shenanigans and manually unpacking Windows   onto a new NTFS partition.

* Boot Windows installer.

* Press `Shift+F10` to open command line.

* Find index of Windows edition you need (replace `X:` with installer stick drive letter if necessary). Home is usually index 1 and Pro is index 6.

        dism /get-wiminfo /wimfile:X:\sources\install.wim

* If NTFS partition is not created yet, create it. You can use setup GUI or `diskpart.exe`.

* Use `diskpart` to assign a disk letter to the EFI partition and the NTFS partition, for example, `E:` and `W:`.

* Unpack the image:

        dism /apply-image /imagefile:X:\sources\install.wim /index:1 /applydir:W:\

* Install EFI boot files:

        bcdboot W:\Windows /s E: /f UEFI

This also skips two "Windows Recovery" partitions that Windows installer creates behind the scenes.

#### Windows on an external hard drive flashes blue screen about `wppRecorder.sys`

This is a temporary problem as of 2018/2019. Microsoft has introduced this bug in build 1809 (October 2018). Download a different build, or copy that driver from a different build (1803 from April 2018 is still available).

#### `blInitializeLibrary` failed

Check if your disk has been turned into MBR/GPT hybrid behind your back. This is easiest done from under Linux by running `gdisk` on the targer drive, and issuing commands:

* `p` to confirm the problem
* `b` to backup partition table to a file (good idea)
* `x` to switch to extended mode
* `n` to create new protective MBR
* `w` to write changes to disk

#### If Time Machine full restore fails

Open the log file. Find the offending file inside the backups DB—most usually it is some Mac App Store `_receipt` file. Start Terminal, delete that file from the backups DB. Retry. Repeat if necessary.

#### Install rEFInd

rEFInd makes booting multiple OSes much more pleasurable experience. Despite the scary amount of text on its website, the installation is trivial:

* Download the rEFInd zip file.

* Mount EFI partition using `diskutil`.

* In the EFI partition, create a folder for rEFInd inside the root `EFI` folder, for example `/Volumes/EFI/EFI/refind`.

* Copy contents of zip there.

* (Optional) Copy `refind.conf-sample` to `refind.conf` and edit it. You probably won't need it as it works out of the box, but there is a useful option for eGPU users that prevents shutting down internal video card when booting non-Mac OSes when booting with eGPU connected.

* Boot Mac in recovery mode (press `Cmd+R` on boot), open Terminal, mount EFI partition and run

        bless --mount /Volumes/EFI --setBoot --file /Volumes/EFI/EFI/refind/refind_x64.efi

You can also delete 32-bit and ARM versions of rEFInd to save space on EFI partition, as Mac only needs `x64`.
