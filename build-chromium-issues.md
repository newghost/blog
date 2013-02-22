Issue list when make a build of chromium.
====


1. Build Chromium on Linux(x86)
====
Based version: chrome - Revision 183078: /trunk/src/chrome/common

Permission Dennied: http://code.google.com/p/chromium/wiki/LinuxBuildInstructions
----

  Caused by fireware, fixed by
  - 404: using wallproxy or VPN. 
  - timeout: re-execute the command from error message, and then sync again.

Build Error
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
    
Solustion: Using stable version, etc using

    using: gclient sync --revision src@183149

Link Error
----

    LINK(target) out/Debug/chrome
    out/Debug/../../third_party/gold/gold32: fatal error: out/Debug/chrome: mmap: failed to allocate 1510416388 bytes for output file: Cannot allocate memory
    collect2: ld returned 1 exit status
    make: *** [out/Debug/chrome] Error 1

  Solustion: Wont fix, re-installed ubuntu 12.04 LTS 64bit instead of 32 bit, according to 
  [Issue Link] (http://code.google.com/p/chromium/issues/detail?id=82056)
  [Issue Link] (http://code.google.com/p/chromium/issues/detail?id=141624)


Lots of 404s when apt-get install after reinstall 64bit system, checked,
----

    sudo apt-get update
    #....
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/multiverse/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/multiverse/i18n/Translation-en  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/restricted/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/restricted/i18n/Translation-en  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/universe/i18n/Translation-en_US  Connection failed
    W: Failed to fetch http://cn.archive.ubuntu.com/ubuntu/dists/precise-backports/universe/i18n/Translation-en  Connection failedf
  
Fixed by: seems caused by the unstable of cn.archive.ubuntu.com, maybe choose American location will not run into this problem.

    sudo gedit /etc/apt/sources.list  #remove all the prefix "cn." in the sources list.

Install depanded library error
----

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

Fixed by: just follow the recommdation, becareful using python2.7-dev instead of python2.5-dev

    sudo apt-get install ttf-mscorefonts-installer libcups2-dev python2.7-dev


The gclient sync failure
----

    kris@kris-pc:~/svn/chromium/src$ ./build/gyp_chromium -Dchromeos=0Updating projects from gyp files...
    Using overrides found in /home/kris/.gyp/include.gypi
    Package libpci was not found in the pkg-config search path.
    Perhaps you should add the directory containing `libpci.pc'
    to the PKG_CONFIG_PATH environment variable
    No package 'libpci' found
    gyp: Call to 'pkg-config --cflags libpci' returned exit status 1. while loading dependencies of /home/kris/svn/chromium/src/base/base.gyp while loading dependencies of /home/kris/svn/chromium/src/build/all.gyp while trying to load /home/kris/svn/chromium/src/build/all.gyp

Fixed by: reference from build/install-build-deps.sh, install depandence mannually: 

    # pick up the package from install-build-deps.sh and execute, the contents in wiki is not the latest.
    sudo apt-get install apache2.2-bin bison curl elfutils fakeroot flex g++ gperf language-pack-fr libapache2-mod-php5 libasound2-dev libbz2-dev libcairo2-dev libcups2-dev libcurl4-gnutls-dev libelf-dev libgconf2-dev libgl1-mesa-dev libglib2.0-dev libglu1-mesa-dev libgnome-keyring-dev libgtk2.0-dev libkrb5-dev libnspr4-dev libnss3-dev libpam0g-dev libpci-dev libsctp-dev libspeechd-dev libsqlite3-dev libssl-dev libudev-dev libwww-perl libxslt1-dev libxss-dev libxt-dev libxtst-dev mesa-common-dev metacity patch perl php5-cgi pkg-config python python-cherrypy3 python-dev python-psutil rpm ruby subversion ttf-dejavu-core ttf-indic-fonts ttf-kochi-gothic ttf-kochi-mincho ttf-thai-tlwg wdiff git-core


Finally it build successfully, go to next step.


2. Cross-compile chromium for Arm
====
[Reference] (https://code.google.com/p/chromium/wiki/LinuxChromiumArm#Recipe2:_Explicit_Cross_compiling)


Install CodeSourcery error:
----

    The installer has detected that your system uses the dash shell
    as /bin/sh.  This shell is not supported by the installer.
    You can work around this problem by changing /bin/sh to be a
    symbolic link to a supported shell such as bash.

Fixed by:

    sudo dpkg-reconfigure -plow dash
    #Install as /bin/sh? No

crosstool-ng, Error when: ./configure,
----
[Reference] (http://jecxjo.motd.org/code/blosxom.cgi/devel/cross_compiler_environment.html)

    sudo apt-get install texinfo

    configure: error: missing required tool: makeinfo
    configure: error: could not find GNU awk

    could not find GNU automake >= 1.10

Resolved ty, follow these [steps] (http://elinux.org/Didj_crosstool-NG_Build_Environment):

    build-essential
    bison
    flex
    texinfo or makeinfo (if you can't apt-get makeinfo try texinfo)
    automake
    libtool
    cvs
    ncurses (libncurses5-dev)

    sudo apt-get install build-essential bison flex texinfo automake libtool cvs libncurses5-dev

ct-ng config error (1.18.0), 
----

    $ ct-ng arm-cortex_a8-linux-gnueabi
    make: *** No rule to make target `arm-none-linux-gnueabi'.  Stop.

Resolved by:
    ct-ng list-samples
    #choose a proper one, etc, i use:

    kris@kris-pc:~/x-tools$ mkdir toolchain-build
    kris@kris-pc:~/x-tools$ cd toolchain-build
    kris@kris-pc:~/x-tools/toolchain-build$ ct-ng armv6-rpi-linux-gnueabi

PPL 404:
----

    [EXTRA]    Retrieving 'ppl-0.11.2'
    [ERROR]   
    [ERROR]  >>
    [ERROR]  >>  Build failed in step 'Retrieving needed toolchain components' tarballs'

  PPL update it's url.

  Observe in proxy: 404 21655
  http://bugseng.com/products/ppl/Download/ftp/releases/0.11.2/ppl-0.11.2.tgz     HTTP/1.1
  It should be:
  http://bugseng.com/products/ppl/download/ftp/releases/0.11.2/ppl-0.11.2.tgz
  
Resolved by, update source codes:

    $ grep http://bugseng.com/products/ppl/Download/ftp/releases/ /home/kris/x-tools/* -r
    #find the location of shell and update it.
    $ sublime /home/kris/x-tools/crosstool-ng/lib/ct-ng.1.16.0/scripts/build/companion_libs/ppl.sh
    # all Download to lower case
    do_ppl_get() {
        CT_GetFile "ppl-${CT_PPL_VERSION}"                                      \
            # here:  http://www.cs.unipr.it/ppl/Download/ftp/releases/${CT_PPL_VERSION}  \
            http://www.cs.unipr.it/ppl/download/ftp/releases/${CT_PPL_VERSION}  \

    # rebuild cross-ng, and it can download ppl from source.


ct-ng build error:
----

Version list of armv6 device:

    [EXTRA]    Extracting 'linux-3.6.11'
    [EXTRA]    Patching 'linux-3.6.11'
    [EXTRA]    Extracting 'gmp-5.0.2'
    [EXTRA]    Patching 'gmp-5.0.2'
    [EXTRA]    Extracting 'mpfr-3.1.0'
    [EXTRA]    Patching 'mpfr-3.1.0'
    [EXTRA]    Extracting 'ppl-0.11.2'
    [EXTRA]    Patching 'ppl-0.11.2'
    [EXTRA]    Extracting 'cloog-ppl-0.15.11'
    [EXTRA]    Patching 'cloog-ppl-0.15.11'
    [EXTRA]    Extracting 'mpc-0.9'
    [EXTRA]    Patching 'mpc-0.9'
    [EXTRA]    Extracting 'binutils-2.22'
    [EXTRA]    Patching 'binutils-2.22'
    [EXTRA]    Extracting 'gcc-linaro-4.7-2013.02'
    [EXTRA]    Patching 'gcc-linaro-4.7-2013.02'
    [EXTRA]    Extracting 'eglibc-2_16'
    [EXTRA]    Patching 'eglibc-2_16'
    [EXTRA]    Extracting 'eglibc-ports-2_16'
    [EXTRA]    Patching 'eglibc-ports-2_16'

    ....

    [ERROR]    make[2]: *** [cc1] Error 1
    [ERROR]    make[1]: *** [all-gcc] Error 2
    [ERROR]   
    [ERROR]  >>
    [ERROR]  >>  Build failed in step 'Installing pass-1 core C compiler'
    [ERROR]  >>        called in step '(top-level)'
    [ERROR]  >>
    [ERROR]  >>  Error happened in: CT_DoExecLog[scripts/functions@257]
    [ERROR]  >>        called from: do_cc_core_backend[scripts/build/cc/gcc.sh@448]
    [ERROR]  >>        called from: do_cc_core_pass_1[scripts/build/cc/gcc.sh@101]
    [ERROR]  >>        called from: main[scripts/crosstool-NG.sh@632]
    [ERROR]  >>
    [ERROR]  >>  For more info on this error, look at the file: 'build.log'
    [ERROR]  >>  There is a list of known issues, some with workarounds, in:
    [ERROR]  >>      '/home/kris/x-tools/crosstool-ng/share/doc/crosstool-ng/ct-ng.hg+default-4e8bfe85da61/B - Known issues.txt'

Seems these error is caused by the latest version of ppl-0.11.2

    [ERROR]     /to `home/std::length_error::~length_error()'kris
    [ERROR]    /kris/home/xkris-/xtools-/toolstoolchain/-buildtoolchain--armv6/build.-build/armv6src//ppl.-0.11.2build//src/srcVariable.inlines.hh/:43ppl: -undefined0.11.2 /reference srcto/ `Temp.inlines.hh:52std::length_error::length_error(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&):' 

Trying fixed by rebuild with A8 device, which with the ppl-0.10.2

    kris@kris-pc:~/x-tools$ rm toolchain-build -r
    kris@kris-pc:~/x-tools$ mkdir toolchain-build
    kris@kris-pc:~/x-tools$ cd toolchain-build
    kris@kris-pc:~/x-tools/toolchain-build$ ct-ng arm-cortex_a8-linux-gnueabi
    kris@kris-pc:~/x-tools/toolchain-build$ ct-ng build.2

Version list of A8 device:
We notice that the version of linux kernal is very new but the version of gcc (4.4.6) is lower than Debian based (4.6.3), will use this build and have a try, wish it works:

    [INFO ]  Extracting and patching toolchain components
    [EXTRA]    Extracting 'linux-3.7.3'
    [EXTRA]    Patching 'linux-3.7.3'
    [EXTRA]    Extracting 'gmp-4.3.2'
    [EXTRA]    Patching 'gmp-4.3.2'
    [EXTRA]    Extracting 'mpfr-2.4.2'
    [EXTRA]    Patching 'mpfr-2.4.2'
    [EXTRA]    Extracting 'ppl-0.10.2'
    [EXTRA]    Patching 'ppl-0.10.2'
    [EXTRA]    Extracting 'cloog-ppl-0.15.9'
    [EXTRA]    Patching 'cloog-ppl-0.15.9'
    [EXTRA]    Extracting 'libelf-0.8.13'
    [EXTRA]    Patching 'libelf-0.8.13'
    [EXTRA]    Extracting 'binutils-2.20.1a'
    [EXTRA]    Patching 'binutils-2.20.1a'
    [EXTRA]    Extracting 'gcc-4.4.6'
    [EXTRA]    Patching 'gcc-4.4.6'
    [EXTRA]    Extracting 'glibc-2.9'
    [EXTRA]    Patching 'glibc-2.9'
    [EXTRA]    Extracting 'glibc-ports-2.9'
    [EXTRA]    Patching 'glibc-ports-2.9'
    [EXTRA]    Extracting 'dmalloc-5.5.2'
    [EXTRA]    Patching 'dmalloc-5.5.2'
    [EXTRA]    Extracting 'duma_2_5_15'
    [EXTRA]    Patching 'duma-2_5_15'
    [EXTRA]    Extracting 'gdb-6.8a'
    [EXTRA]    Patching 'gdb-6.8a'
    [EXTRA]    Extracting 'ncurses-5.9'
    [EXTRA]    Patching 'ncurses-5.9'
    [EXTRA]    Extracting 'expat-2.1.0'
    [EXTRA]    Patching 'expat-2.1.0'
    [EXTRA]    Extracting 'ltrace-0.5.3'
    [EXTRA]    Patching 'ltrace-0.5.3'
    [EXTRA]    Extracting 'strace-4.5.19'
    [EXTRA]    Patching 'strace-4.5.19'

Build rootfs
----
Install packages for arm cross toolchain, these content is out of date in offical wiki, 
find the arm_list in chromium/src/build/install-build-deps.sh:

    # arm cross toolchain packages needed to build chrome on arm
    arm_list="libc6-armel-cross libc6-dev-armel-cross libgcc1-armel-cross
              libgomp1-armel-cross linux-libc-dev-armel-cross
              libgcc1-dbg-armel-cross libgomp1-dbg-armel-cross
              binutils-arm-linux-gnueabi cpp-arm-linux-gnueabi
              gcc-arm-linux-gnueabi g++-arm-linux-gnueabi
              libmudflap0-dbg-armel-cross"
              
Install manually:

    sudo apt-get install libc6-armel-cross libc6-dev-armel-cross libgcc1-armel-cross libgomp1-armel-cross linux-libc-dev-armel-cross      libgcc1-dbg-armel-cross libgomp1-dbg-armel-cross binutils-arm-linux-gnueabi cpp-arm-linux-gnueabi gcc-arm-linux-gnueabi g++-arm-linux-gnueabi libmudflap0-dbg-armel-cross


Set toolchain environment variables: 
----

    # make a directory to build rootfs
    mkdir ~/svn/chromium/rootfs
    sublime $HOME/.bashrc
    #add 
    # crosstool
    export PATH=$HOME/x-tools/crosstool-ng/bin:$PATH
    # crosstool-build
    export GYP_DEFINES="target_arch=arm sysroot=~/svn/chromium/rootfs linux_use_tcmalloc=0 armv7=1 arm_thumb=1"
    export CROSSTOOL=~/x-tools/arm-cortex_a8-linux-gnueabi/bin/arm-cortex_a8-linux-gnueabi
    export CXX=$CROSSTOOL-g++
    export CC=$CROSSTOOL-gcc
    export AR=$CROSSTOOL-ar
    export AS=$CROSSTOOL-as
    export RANLIB=$CROSSTOOL-ranlib
    
After edit the bashrc, it's better test the enviroment variables by ls, etc:

    kris@kris-pc:~$ ls $RANLIB
    /home/kris/x-tools/arm-cortex_a8-linux-gnueabi/bin/arm-cortex_a8-linux-gnueabi-ranlib

Build as normal
----

    make -r -j2 BUILDTYPE=Release chrome

Error: 

      TOUCH out/Release/obj.target/third_party/mesa/mesa_headers.stamp
      CXX(target) out/Release/obj.target/v8_base/v8/src/accessors.o
    In file included from v8/src/../include/v8.h:44,
                     from v8/src/v8.h:52,
                     from v8/src/accessors.cc:28:
    v8/src/../include/v8stdint.h:34:19: warning: stdio.h: No such file or directory
    v8/src/../include/v8stdint.h:50:20: warning: stdint.h: No such file or directory
    In file included from v8/src/v8globals.h:32,
                     from v8/src/v8.h:53,
                     from v8/src/accessors.cc:28:
    v8/src/checks.h:31:20: warning: string.h: No such file or directory
    In file included from v8/src/v8utils.h:31,
                     from v8/src/v8.h:56,
                     from v8/src/accessors.cc:28:
    v8/src/utils.h:31:20: warning: stdlib.h: No such file or directory
    In file included from v8/src/unicode-inl.h:31,
                     from v8/src/objects.h:37,
                     from v8/src/elements.h:32,
                     from v8/src/objects-inl.h:38,
                     from v8/src/v8.h:60,
                     from v8/src/accessors.cc:28:
    v8/src/unicode.h:31:23: warning: sys/types.h: No such file or directory
    In file included from v8/src/elements.h:33,
                     from v8/src/objects-inl.h:38,
                     from v8/src/v8.h:60,
                     from v8/src/accessors.cc:28:
    v8/src/heap.h:31:18: warning: math.h: No such file or directory
    In file included from v8/src/v8.h:64,
                     from v8/src/accessors.cc:28:
    v8/src/mark-compact-inl.h:32:20: error: memory.h: No such file or directory
    In file included from v8/src/v8.h:52,
                     from v8/src/accessors.cc:28:
    v8/src/../include/v8.h:478: error: 'uint16_t' has not been declared
    v8/src/../include/v8.h:484: error: 'uint16_t' has not been declared
    v8/src/../include/v8.h:487: error: 'uint16_t' does not name a type
    v8/src/../include/v8.h:493: error: 'uint16_t' does not name a type
    v8/src/../include/v8.h:822: error: 'FILE' has not been declared

Blocked by this error, will check these:
  1. incompative of G++ or C library?
  2. unstable version of chromium?
