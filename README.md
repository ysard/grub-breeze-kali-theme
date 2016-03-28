Theme for GRUB inspired by Kali Linux distribution and Breeze theme for KDE


The theme that I suggest is largely inspired by a [work](https://github.com/gustawho/grub2-theme-breeze),
of *Gustavo Castro*, licensed under a **Creative Commons Attribution-ShareAlike 3.0 License**.
This theme is therefore distributed under the **same license**.

Configuration is quite quick; Copy and paste variables to modify the grub file (`/etc/default/grub`) 
and add the classes to the right place in the files in `/etc/grub.d/`.


# Installation

First, clone the git repository:

    git clone git@github.com:ysard/grub-breeze-kali-theme.git

You have to put the files in `/boot/grub/themes/my_theme`.
Remember the folder, it will be reused later.

Note that the main wallpaper (`kali-wallpaper_1920x1080.png`) is quite heavy (4,5Mo),
you might want to reduce it.


# GRUB configuration

**All settings of this part will be in the file `/etc/default/grub`.**

## Define the theme

Modify the following variable:

    GRUB_THEME=/boot/grub/themes/my_theme/theme.txt

    
## Define the screen resolution

You have to get the maximum resolution supported for your system.

Two solutions:

* Via the grub console (by pressing 'c' on the OS selection screen)
* Via this command supposed to give resolutions supported by the framebuffer:

    `sudo hwinfo --framebuffer`
    
    
Then, you have to modify the foolowing variables:

    GRUB_GFXMODE=1920x1080x32,1920x1080,1024x768x32,auto

And potentially:

    GRUB_GFXPAYLOAD=keep
    GRUB_GFXPAYLOAD_LINUX=1920x1080x24,1024x768x32
    #keep: preserve graphic mode set by GRUB_GFXMODE

*Note: If you use CSM Bios ([Compatibility Support Module](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface#CSM_booting), an emulation of old Bios) 
for a reason or another, your screen resolution may be much lower than the native resolution.  
You can't do much. The quality of Bios implemented in the machines 
varies widely, and don't rely on manufacturers to provide patches or useful functions for this kind of software.*



## Define the decoration of the GRUB terminal

This setting can't be configured by the custom theme.  
Please note that:

* An image for the console background should not be transparent.
* By default there will be black screen.
* Coordinates of the console are constant and hard-coded.

Variable:

    GRUB_BACKGROUND="/boot/grub/themes/my_theme/background-1024.png"


If you want to set your own image, you will have to convert it:

    convert image_example.jpg -resize 1024x768! -depth 16 correct_format.jpg


# Icons

TL,DR: Icon's name should correspond to some predefined class.

Default classes:

    windows > os
    gnu-linux > gnu > os
    osx > darwin > os
    hurd > gnu > os

More important class is on the left. If icon `windows.png` is found then it will be shown.
In this case, the icon `os.png` will not.

Main system's class (equal to it's name) will be available also.
Main system in this case, is the system where `update-grub` script was called.
This class is more important than `gnu-linux`.

Classes (and current system's class) are readable in `/boot/grub/grub.cfg`.
However, a large amount of icons is already included in the custom theme.


## Define icons for submenus & UEFI entries

You will have to edit some files in `/etc/grub.d/`. 
These files are used by `grub-mkconfig` during the generation of `grub.cfg`.

The files you are interested in are:

    10_linux*
    30_uefi-firmware*

The `10_linux` file is for linux operating system entries;  
The `30_uefi-firmware` file is for the uefi setup entry.

First, in `10_linux`:

Original line:

    echo "submenu '$(gettext_printf "Advanced options for %s" "${OS}" | grub_quote)' \$menuentry_id_option 'gnulinux-advanced-$boot_device_id' {"
    
Add the `${CLASS}` option so that it now looks like this:

    echo "submenu '$(gettext_printf "Advanced options for %s" "${OS}" | grub_quote)' ${CLASS} \$menuentry_id_option 'gnulinux-advanced-$boot_device_id' {"

<br>
    
Finally, in `30_uefi-firmware`:

Add a class entry, immediately after the export entries at the top of the file:

    CLASS="--class recovery"

and add the `${CLASS}` option in the menuentry line:

    menuentry '$LABEL' ${CLASS} \$menuentry_id_option 'uefi-firmware'

<br>

*Note:
The class entry has to match an icon that is in the theme's icon folder.*

    /boot/grub/themes/<theme name>/icons/




# Commit your changes

You must regenerate the GRUB's menu and commit changes:

    grub-mkconfig -o /boot/grub/grub.cfg
    update-grub
    
