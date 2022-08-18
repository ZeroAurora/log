# Reset EFI GRUB in Fedora

## Cause & Symptom

1. I installed official Nvidia drivers and CUDA toolkit, but when I booted the computer later, I couldn't enter GUI, and couldn't switch tty.
2. I entered runlevel 3 and removed the drivers. The system operated normally again, except that ABRT kept complaining about Nouveau, the community-driven free driver. But I didn't even have Nouveau installed. I guess it's GRUB's problem, so I deleted the whole `/etc/default/grub` file (I regert not backing this up), which contains Nvidia's inserts. Unfortunately, it didn't work. Since settings were deleted, **I tried to use `grub-customizer` to restore them**, which made things broken: **GRUB's fonts corrupted**. And Nouveau's problem still existed.
3.  **I removed `/boot/efi/EFI/fedora/`** and tried to follow [Fedora's documentation](https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/kernel-module-driver-configuration/Working_with_the_GRUB_2_Boot_Loader/#sec-Reinstalling_GRUB_2) to reinstall GRUB. Then things got even worse: GRUB couldn't find its configuration and entered rescue cmdline. After trying, I found that **now GRUB only recognized configurations and fonts in EFI GRUB folder (`/boot/efi/EFI/fedora/`)**, which was strange, and I didn't found any solutions to similar problems online.

## Fix

The fix is, however, very easy. Reinstall GRUB. (Wait, haven't I reinstalled it before?)

Well, before reinstalling, two files are also needed to be removed:

```
# rm /boot/efi/EFI/fedora/grub.cfg
# rm /boot/grub2/grub.cfg
```

"The removal of the two `grub.cfg` files will trigger a script in `grub2-common` to recreate these files", [Fedora Wiki](https://fedoraproject.org/wiki/GRUB_2#Instructions_for_UEFI-based_systems) said. And then...

```
# dnf reinstall shim-* grub2-efi-* grub2-common
```

Done.

Why this happened? Because I accidentally deleted `/boot/efi/EFI/fedora/grub.cfg`, **breaking the redirection to /boot/grub2/grub.cfg**.

```
# cat /boot/efi/EFI/fedora/grub.cfg
search --no-floppy --fs-uuid --set=dev [PARTITION_UUID]
set prefix=($dev)/grub2
export $prefix
configfile $prefix/grub.cfg
```

It turns out that Fedora's documentation on reinstalling GRUB is incomplete. It doesn't explain what to do when `/boot/efi/EFI/fedora/grub.cfg` is broken.

As for `grub-customizer`, the problem is that it modifies `/boot/efi/EFI/fedora/grub.cfg` by default, breaking redirections and resulting GRUB unable to find fonts. A bug report is planned.

## Appendix

And reinstalling GRUB fixed the Nouveau warning. I didn't find out why. Does it relate to the steps described in Fedora Documentation (deleting other config folders)? Well, it may just remain a mystery.

(EOF)
