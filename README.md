# fglrx-for-Fedora
Patches for the AMD fglrx proprietary video driver 15.302 for installing on Fedora 23 and up, kernel versions 4.4.* and later

The described process and patches in this repository work through at least Fedora Core 26 and kernel 4.11.9-300.

In addition to patches, this repository also contains a convenience "do_install" script. Once you have generated your fglrx-15.302 directory and copied all the files from this git repository there, you can run the do_install script which will extract the install, patch the files depending on your kernel level and run the install. The problem is that this must be run as root. I wrote this for my own convenience, but urge you to read and understand the contents - for all you know, through either evil or incompetence, I could be messing up your machine beyond repair. The directions below assume you are doing this by hand.

I update this README file fairly sporadically, but the patches should be current, since my own machine, a 64 bit system with a R7 370 card, won't work without a current patch. I run KDE Plasma. I would suspect that the same process would work fine for a 32 bit machine as well, provided you download the 32 bit driver version.

All the patches are cumulative, meaning that you need only to apply the latest patch. Check out the patch selection logic in the do_install script if you are unsure which patch to install. By and large, if your kernel is 4.x, then use patch 4.y, where "y" is the largest available patch number where y<=x.

I tend to travel quite a bit, so I might not be at home respond to email or to update the patches for a month or two. Check out the community discussion page at https://bluehatrecord.wordpress.com/2016/03/25/installing-the-proprietary-amd-crimson-driver-on-fedora-23-with-linux-kernel-4-4-6-300/ (long discussion, scroll down to the bottom) to see if there are any changes after the latest patch here. It's also a good place to ask questions if you run into trouble. New patches are usually found by A.I. (thanks!) and anounced there and I just roll them in and publish them here.

Any time you upgrade to a new kernel, it's a good idea to rebuild yout fglrx - it's not always necessary, but it often is. Even if the patches haven't changed, some other component might have and your driver may either stop working or work very slowly.

The rest of this README summarizes the excellent instructions from the https://bluehatrecord.wordpress.com/2016/03/25/installing-the-proprietary-amd-crimson-driver-on-fedora-23-with-linux-kernel-4-4-6-300/ , as modified by the discussion for later kernel versions.

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
- find the right patch, based on yout kernel version. The patch minor version shoud be the latest possible, but not bigger than your current kernel version.
- if your patch file is fglrx_kernel_4.X.diff patch the code by doing: patch -p1 < ../fglrx_kernel_4.X.diff
- You are now ready to install the driver. The build and install must be done as root, reportedly "sudo" is not enough. As root, change to the install directory.
- run: ./ati-installer.sh 15.302 --install
- when you get the install window, select the second option: "Install driver 15.302 on X.Org 6.9 or later 64-bit"
- accept the defaults and let it install
- skip reboot when it asks at the end (no problem if you reboot, it just takes little longer)
- when it comes back, as root, run: aticonfig --initial
- reboot
- you "should" be good to go! But, if you're like me, it will take a week before you get it all figured out!
