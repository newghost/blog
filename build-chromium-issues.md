Issue list when make a local build of chromium.
====


1. Build Chromium in Linux(x86)
====
For version: chrome - Revision 183078: /trunk/src/chrome/common

* Permission Dennied: http://code.google.com/p/chromium/wiki/LinuxBuildInstructions
  Caused by fireware, fixed by
  - 404: using wallproxy or VPN. 
  - timeout: re-execute the command from error message, and then sync again.

* Fixed by rename http://src.chromium.org/chrome/trunk/src/chrome/common/chrome_version_info_posix.h.version
----
    input_extension_api.o
      CXX(target) out/Debug/obj.target/browser/chrome/browser/nacl_host/nacl_browser.o
      CXX(target) out/Debug/obj.target/browser/chrome/browser/nacl_host/nacl_infobar.o
      CXX(target) out/Debug/obj.target/browser/chrome/browser/nacl_host/nacl_process_host.o
      CXX(target) out/Debug/obj.target/browser/chrome/browser/nacl_host/nacl_validation_cache.o
      CXX(target) out/Debug/obj.target/browser/chrome/browser/nacl_host/pnacl_file_host.o
      CXX(target) out/Debug/obj.target/browser/chrome/app/breakpad_linux.o
    chrome/app/breakpad_linux.cc:43:53: fatal error: chrome/common/chrome_version_info_posix.h: No such file or directory
    compilation terminated.
    make: *** [out/Debug/obj.target/browser/chrome/app/breakpad_linux.o] Error 1

* Samiliar Issue: Wont fix, re-installed ubuntu 12.04 LTS 64bit
http://code.google.com/p/chromium/issues/detail?id=82056
http://code.google.com/p/chromium/issues/detail?id=141624

    LINK(target) out/Debug/chrome
    out/Debug/../../third_party/gold/gold32: fatal error: out/Debug/chrome: mmap: failed to allocate 1510416388 bytes for output file: Cannot allocate memory
    collect2: ld returned 1 exit status
    make: *** [out/Debug/chrome] Error 1


* Lots of 404s when apt-get install, checked sudo apt-get update
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/multiverse/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/multiverse/i18n/Translation-en  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/restricted/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/restricted/i18n/Translation-en  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/universe/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/universe/i18n/Translation-en  Connection failedf
Seems caused by the unstable of cn.archive.ubuntu.com, maybe choose American location will not run into this issue.
Fixed by: sudo gedit /etc/apt/sources.list, remove all the prefix "cn." in the sources list.


* depandeds


    kris@kris-pc:~$ sudo apt-get install bison fakeroot flex g++ g++-multilib gperf \
    >   libapache2-mod-php5 libasound2-dev libbz2-dev libcairo2-dev \
    >   libdbus-glib-1-dev libgconf2-dev libgl1-mesa-dev libglu1-mesa-dev \
    >   libglib2.0-dev libgtk2.0-dev libjpeg62-dev libnspr4-dev libnss3-dev \
    >   libpam0g-dev libsqlite3-dev libxslt1-dev libxss-dev \
    >   mesa-common-dev msttcorefonts patch perl pkg-config python \
    >   python2.5-dev rpm subversion libcupsys2-dev libgnome-keyring-dev \
    >   libcurl4-gnutls-dev libelf-dev libc6-i386 lib32stdc++6 \
    >   libbluetooth-dev
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    Note, selecting 'ttf-mscorefonts-installer' instead of 'msttcorefonts'
    Note, selecting 'libcups2-dev' instead of 'libcupsys2-dev'
    Package python2.5-dev is not available, but is referred to by another package.
    This may mean that the package is missing, has been obsoleted, or
    is only available from another source
    However the following packages replace it:
      python-old-doctools

Fixed by: just follow the recommdation 
    sudo apt-get install ttf-mscorefonts-installer libcups2-dev python2.7-dev

* version
  using: gclient sync --revision src@183149


* configuration

    kris@kris-pc:~/svn/chromium/src$ ./build/gyp_chromium -Dchromeos=0Updating projects from gyp files...
    Using overrides found in /home/kris/.gyp/include.gypi
    Package libpci was not found in the pkg-config search path.
    Perhaps you should add the directory containing `libpci.pc'
    to the PKG_CONFIG_PATH environment variable
    No package 'libpci' found
    gyp: Call to 'pkg-config --cflags libpci' returned exit status 1. while loading dependencies of /home/kris/svn/chromium/src/base/base.gyp while loading dependencies of /home/kris/svn/chromium/src/build/all.gyp while trying to load /home/kris/svn/chromium/src/build/all.gyp


    1. The gclient sync failure (outside chroot) was fixed by running build/install-build-deps.sh outside the chroot.  -- won't fix

    2. Install depandence mannually: 
    pick up the page reference install-build-deps.sh and execute:

    sudo apt-get install apache2.2-bin bison curl elfutils fakeroot flex g++ gperf language-pack-fr libapache2-mod-php5 libasound2-dev libbz2-dev libcairo2-dev libcups2-dev libcurl4-gnutls-dev libelf-dev libgconf2-dev libgl1-mesa-dev libglib2.0-dev libglu1-mesa-dev libgnome-keyring-dev libgtk2.0-dev libkrb5-dev libnspr4-dev libnss3-dev libpam0g-dev libpci-dev libsctp-dev libspeechd-dev libsqlite3-dev libssl-dev libudev-dev libwww-perl libxslt1-dev libxss-dev libxt-dev libxtst-dev mesa-common-dev metacity patch perl php5-cgi pkg-config python python-cherrypy3 python-dev python-psutil rpm ruby subversion ttf-dejavu-core ttf-indic-fonts ttf-kochi-gothic ttf-kochi-mincho ttf-thai-tlwg wdiff git-core




2. Build log for Arm
====
https://code.google.com/p/chromium/wiki/LinuxChromiumArm#Recipe2:_Explicit_Cross_compiling


> Install CodeSourcery error:


    The installer has detected that your system uses the dash shell
    as /bin/sh.  This shell is not supported by the installer.
    You can work around this problem by changing /bin/sh to be a
    symbolic link to a supported shell such as bash.

    sudo dpkg-reconfigure -plow dash
    #Install as /bin/sh? No

> crosstool-ng

Error when: ./configure

    sudo apt-get install texinfo

    configure: error: missing required tool: makeinfo
    configure: error: could not find GNU awk

    could not find GNU automake >= 1.10


Resolved ty: 
http://elinux.org/Didj_crosstool-NG_Build_Environment

Prerequisites

    build-essential
    bison
    flex
    texinfo or makeinfo (if you can't apt-get makeinfo try texinfo)
    automake
    libtool
    cvs
    ncurses (libncurses5-dev)

    sudo apt-get install build-essential bison flex texinfo automake libtool cvs libncurses5-dev

ct-ng: Error (1.18.0)

    $ ct-ng arm-cortex_a8-linux-gnueabi
    make: *** No rule to make target `arm-none-linux-gnueabi'.  Stop.

Resolved by:

    ct-ng list-samples
    #choose a proper one, etc, i use:

    kris@kris-pc:~/x-tools$ mkdir toolchain-build
    kris@kris-pc:~/x-tools$ cd toolchain-build
    kris@kris-pc:~/x-tools/toolchain-build$ ct-ng armv6-rpi-linux-gnueabi


PPL 404:

    [EXTRA]    Retrieving 'ppl-0.11.2'
    [ERROR]   
    [ERROR]  >>
    [ERROR]  >>  Build failed in step 'Retrieving needed toolchain components' tarballs'

Resolved by:

    #PPL update it's url.

    Observe in proxy: 404 21655
    http://bugseng.com/products/ppl/Download/ftp/releases/0.11.2/ppl-0.11.2.tgz     HTTP/1.1
    It should be:
    http://bugseng.com/products/ppl/download/ftp/releases/0.11.2/ppl-0.11.2.tgz

    $ grep http://bugseng.com/products/ppl/Download/ftp/releases/ /home/kris/x-tools/* -r
    #find the location of shell and update it.
    $ sublime /home/kris/x-tools/crosstool-ng/lib/ct-ng.1.16.0/scripts/build/companion_libs/ppl.sh
    # all Download to lower case
    do_ppl_get() {
        CT_GetFile "ppl-${CT_PPL_VERSION}"                                      \
            # here:  http://www.cs.unipr.it/ppl/Download/ftp/releases/${CT_PPL_VERSION}  \
            http://www.cs.unipr.it/ppl/download/ftp/releases/${CT_PPL_VERSION}  \

    # rebuild cross-ng


Appendix:
====

* crolltool-ng config: http://jecxjo.motd.org/code/blosxom.cgi/devel/cross_compiler_environment.html
