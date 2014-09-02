
###Notes
* use dracut and grub2-mkconfig normally, external loaders will chain to the grub.cfg inside the imagefile
* boot process in VM: /boot/grub/grub.cfg copied to (sda boot part)/boot/grub/grub.cfg
* boot process from windows boot loader: g2ldr.mbr (BOOTSECTOR type bcdedit entry) -> \g2ldr with chosen embedded cfg (see top line of *.cfg for appropriate image generation command)
* g2ldr.mbr i got from grub4dos or something, i cant find exactly how to rebuild it from source, but it essentially just loads \g2ldr

* this copies the .mod files (just embed modules in image for cleaner fs):
    ```bash
    rsync -aR /usr/lib/./grub/i386-pc boot --include=*.mod --include=*.lst --exclude=*/*/*
    ```
* make a fat image like this (du -sh /usr/lib/gru | sort -n to pick away largest mods)
    ```bash
    cat /usr/lib/grub/i386-pc/lnxboot.img <(find /usr/lib/grub -name *.mod | xargs -i bash -c 'basename {} | sed s/.mod//' | ack -v 'usb|video|vga|cpio|raid|ufs|ext|pxe|regexp|net|gcry|zfs|gdb' | xargs grub-mkimage -O i386-pc ) > g2ldr
    ```
* g2ldr should be produced by same build of grub as in gentoo.xfs! that way in there, grub2-install /dev/null will make all modules available once loop is set as prefix

### TODO
* fix console not working on physicalnile fbset or something? need fb drivers?

* checkin fsimg to github
* generalize ,error check the fsimg module code
* eventuall support shutdown hook for systemd

* find a way to not have to touch /run/initramfs/.need_shutdown

* update grub win to 2.0+ and find a sleeker more reliable way to install it
* formalize the grub.cfg, use searh --uuid and vars etc

* strip down the xfs part some?
* remove nfs mounting

* wrap everything into a build from scratch system using catalyst to make the gentoo.xfs?
* remember to explore using set_env and load_env if passing vars becomes an issue again.
