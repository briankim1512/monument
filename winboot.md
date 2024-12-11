# Incident
I was creating a Linux Mint external disk due to the company I was working at the time. The company, in their infinite wisdom, decided it was a good idea to restrict everything possible on the PC such as uploading, downloading, viewing files, viewing websites, restricting installations of nearly every program in existence, so on and so forth.

Therefore I thought it was prudent to create my own external disk I could boot into so I can bypass all of the security measures and still access the files within the local PC (which for some reason was not encrypted). 

Everything went well until I was at the installation phase of the process where instead of installing the GRUB loader on the external drive, I fat fingered it on the local drive.

This caused me to erroneously believe that there were two bootloaders on the local drive and I deleted the main EFI bootloader off the local drive. This caused not only GRUB loader to disappear, but also Windows boot manager as well.

# Issue
Windows boot manager was completely removed from my disk causing the BIOS to believe that there were no bootable disks within the PC. Not only does this cause the disk to become a "dead" disk, but it also causes live Windows USB to not properly detect where the existing Windows installation is.

# Solution
After an ungodly amount of time trudging through the cursed lands of Microsoft forums and the toxic swamps of corporate solution sites, I managed to find one particular source that explained how to remake the Windows boot manager from a live Windows USB. The process is as follows:

1. Create a live Windows USB of the same type of the "dead" Windows installation.

2. Boot into the live USB and enter the CMD prompt through troubleshooting mode. (Shift + F10 while booting also works)

3. Run the following commands to discern where the Windows installation partition is:
    - `diskpart` - this enters the `diskpart` utility
    - `list disk` - shows the disks connected to the pc
    - `select disk #` - tells `diskpart` to select the disk `#` to modify
    - `list partition` - shows the partitions in disk `#`
    - `select partition n` tells `diskpart` to select partition `n` to modify

4. Once the appropriate partition is selected, shrink this partition to allow space for the new Windows boot manager to reside.
    - `shrink desired=500` - shrink partition by 500 mb

5. Create the EFI partition for Windows boot manager
    - `create partition efi` - create the EFI partition
    - `format fs=fat32 quick` - format the partition into FAT 32 bit file system
    - `assign letter=Y` - assign Y drive to the EFI partition
    - `exit` - exit `diskpart` utility

6. Install Windows boot manager to drive Y (EFI partition)
    - `bcdboot C:\windows /s Y:`

Once these steps are complete, shutdown the system, unplug the USB and pray to whatever god you believe in that this worked.

# Remarks

This is the first documented case of a monument that I created. This is a remainder that I am an idiot and that some things just happen because I failed to pay attention. I hope whoever sees this can swiftly solve their issues and proceed with their day, for this consumed one entirety of mine. 