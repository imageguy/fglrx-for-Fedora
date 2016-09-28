# fglrx-for-Fedora
Patches for the AMD fglrx proprietary video driver 15.302 for installing on Fedora 23, kernel versions 4.4.* and later

In addition to patches, this repository also contains a convenience "do_install" script. Once you have generated your fglrx-15.302 directory and copied all the files from this git repository there, you can run the do_install script which will extract the install, patch the files depending on your kernel level and run the install. The problem is that this must be run as root. I wrote this for my own convenience, but urge you to read and understand the contents - for all you know, through either evil or incompetence, I could be messing up your machine beyond repair. The directions below assume you are doing this by hand.

To install the proprietary fglrx driver for various Radeon/ATI/AMD video cards, the source must be patched. The patch has changed for the kernel 4.4.*, so the patch available at https://gist.githubusercontent.com/skarap/8843be76d286fd53835c/raw/a07d3608249bc7d95e3c9c12b4c402ebd2c9f21a/fglrx_kernel_4.3.diff is no longer quite enough. The fglrx_kernel_4.4.diff patch works through kernel 4.5.7-202. Starting with the kernel 4.6.4 on either fc23 or fc24, you will need fglrx_kernel_4.6.diff patch, as there are two more changes in the source. Starting with kernel 4.7, you will need the fglrx_kernel_4.7.diff patch.

The "4.4" patch here worked for 4.4.4 and up to kernel 4.5.7 kernels on my Fedora 23 64 bit machine with a R7 370 card. The "4.6" patch works on my Fedora 24 with 4.6.4-301, but should work on the 4.6.4 on Fedora 23, as the corresponding include files are the same between the two. This patch works on all 4.6.* kernels, but not on the 4.7.* kernels.

The "4.7" patch adds the patch 4.7-arch-cpu_has_pge-v2.patch from 'git clone https://aur.archlinux.org/catalyst.git/' (thanks to A.I. for finding it!) to the "4.6" patch and works at least up to 4.7.4-200 on Fedora 24.

I run KDE Plasma. I would suspect that the same process would work fine for a 32 bit machine as well, provided you download the 32 bit driver version.

Applying kernel updates often doesn't require rebuilding fglrx, but if the update changes any prerequisites, fglrx will stop working properly. Such was the case with 4.5.5-201 update, which refreshed mesa (libgl). Uninstall fglrx by going to /usr/share/ati and running, as root, amd-uninstall.sh, then rebuild and reinstall fglrx as described below. Many, many thanks to A.I. who pointed this out when the update broke my machine.

The rest of this README summarizes instructions from the excellent directions at https://bluehatrecord.wordpress.com/2016/03/25/installing-the-proprietary-amd-crimson-driver-on-fedora-23-with-linux-kernel-4-4-6-300 as modified by the discussion on the bottom.

- You must have the card installed for the driver install to work.
- Copy the version.h header file:  sudo cp /usr/include/linux/version.h /lib/modules/`uname -r`/build/include/linux/
- fglrx driver does not work  with the Xorg server versions beyond 1.17, so you must downgrade the server to Fedora 21 in the following two steps:
  - sudo dnf downgrade –-allowerasing –-releasever=21 xorg-x11-server-Xorg xorg-x11-server-common
  - original directions had downgrade to F22 and version 1.17, but F22 was upgraded to 1.18 and people report downgrading to version 1.16 on F21 works fine.
  - add the following line to your /etc/yum.repos.d/fedora* files: exclude=xorg-x11-server-Xorg xorg-x11-server-common
  - if the above doesn't work and you still upgrade the packages, just add the following line to your /etc/dnf/dnf.conf file: exclude=xorg-x11-server-Xorg xorg-x11-server-common
- download the driver zip from: http://support.amd.com/en-us/download/desktop?os=Linux+x86_64
- unzip the file to get fglrx-15.302 directory.
- download all the files from this repository's master branch to the fglrx-15.302 directory.
- change to the fglrx-15.302 directory and do: amd-driver-installer-15.302-x86.x86_64.run --extract
    this will make a fglr-install.<random string> directory.
- change to the install directory 
- if your kernel is 4.4.4 or later, but before 4.6.4 patch the code by doing: patch -p1 < ../fglrx_kernel_4.4.diff
- if your kernel is 4.6.4 or later, patch the code by doing: patch -p1 < ../fglrx_kernel_4.6.diff
- if your kernel is 4.7.2 or later, patch the code by doing: patch -p1 < ../fglrx_kernel_4.7.diff
- You are now ready to install the driver. The build and install must be done as root, reportedly "sudo" is not enough. As root, change to the install directory.
- run: ./ati-installer.sh 15.302 --install
- when you get the install window, select the second option: "Install driver 15.302 on X.Org 6.9 or later 64-bit"
- accept the defaults and let it install
- skip reboot when it asks at the end (no problem if you reboot, it just takes little longer)
- when it comes back, as root, run: aticonfig --initial
- reboot
- you "should" be good to go! But, if you're like me, it will take a week before you get it all figured out!
