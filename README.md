# fglrx-for-Fedora
Patch for the AMD fglrx proprietary video driver 15.302 for installing on Fedora 23, kernel versions 4.4.*

To install the proprietary fglrx driver for various Radeon/ATI/AMD video cards, the source must be patched. The patch has changed for the kernel 4.4.*, so the patch available at https://gist.githubusercontent.com/skarap/8843be76d286fd53835c/raw/a07d3608249bc7d95e3c9c12b4c402ebd2c9f21a/fglrx_kernel_4.3.diff is no longer quite enough. The patch here worked for 4.4.4 amd 4.4.5 kernels on my Fedora 23 64 bit machine with a R7 370 card. I run KDE Plasma. I would suspect that the same process would work fine for a 32 bit machine as well, provided you download the 32 bit driver version.

The rest of this README summarizes instructions from the excellent directions at https://bluehatrecord.wordpress.com/2016/01/10/installing-the-proprietary-amd-crimson-driver-on-fedora-23-with-linux-kernel-4-2-8/, as modified by the discussion on the bottom.

- You must have the card installed for the driver install to work.
- Copy the version.h header file:  sudo cp /usr/include/linux/version.h /lib/modules/`uname -r`/build/include/linux/
- fglrx driver does not work  with the Xorg server versions beyond 1.17, so you must downgrade the server to Fedora 22 in the following two steps:
  - sudo dnf downgrade xorg-x11-server-Xorg xorg-x11-server-common –-allowerasing –-releasever=22
  - add the following line to your /etc/yum.repos.d/fedora* files: exclude=xorg-x11-server-Xorg xorg-x11-server-common
  - if the above doesn't work and you still upgrade the packages, just add the following line to your /etc/dnf/dnf.conf file: exclude=xorg-x11-server-Xorg xorg-x11-server-common
- download the driver zip from: http://support.amd.com/en-us/download/desktop?os=Linux+x86_64
- unzip the file to get fglrx-15.302 directory.
- change to the fglrx-15.302 directory and do: amd-driver-installer-15.302-x86.x86_64.run --extract
    this will make a fglr-install.<random string> directory.
- change to the install directory and copy the file patch file fglrx_kernel_4.4.diff that you can get from this repository there
- patch the code by doing: patch -p1 < fglrx_kernel_4.4.diff
- You are now ready to install the driver. The build and install must be doen as root, reportedly "sudo" is not enough. As root, change to the install directory.
- run: ./amd-installer.sh 15.302 --install
- when you get the install window, select the second option: "Install driver 15.302 on X.Org 6.9 or later 64-bit"
- accept the defaults and let it install
- reboot when it asks at the end
- you "should" be good to go! But, if you're like me, it will take a week before you get it all figured out!
