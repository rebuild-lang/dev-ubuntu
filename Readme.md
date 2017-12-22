# Development Ubuntu

This developer setup is based on [Vagrant](https://www.vagrantup.com/).
I use it on Windows with [Virtualbox](https://www.virtualbox.org/).

It uses the Ubuntu 16.04 (Xenial) base box from [bento](https://app.vagrantup.com/bento/boxes/ubuntu-16.04). The official ubuntu box fails to run properly with Vagrant.

## Provisioning

### Install Desktop

* `vagrant up`
* `vagrant ssh`
* `sudo -i`
* `apt update && apt upgrade -yy` - update the system
* `apt install ubuntu-desktop -yy` - install desktop
* `passwd vagrant` - set a password for vagrant user

You should now see the desktop and be able to login

### Bone Keyboard Layout

* `wget https://wiki.neo-layout.org/raw-attachment/wiki/Bone2/xkb.tar.gz`
* `cd /usr/share/X11/`
* `sudo tar -xzf ~vagrant/xkb.tar.gz`
* add `setxkbmap de bone` as autostart (type autostart in search)

### Install Clang 5.0

* `wget -O - http://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -`
* `apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-5.0 main"`
* `apt update`
* `apt install clang-5.0 clang-format-5.0 lldb-5.0 lld-5.0 libclang-5.0-dev subversion cmake -y`

### Install Clang 6.0 (dev)

* `apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial main"`
* `apt update`
* `apt install clang-6.0 clang-format-6.0 lldb-6.0 lld-6.0 libclang-6.0-dev -y`

### Compile Libc++ from SVN

* `cd /tmp`
* `svn co http://llvm.org/svn/llvm-project/libcxx/branches/release_50/ libcxx`
* `svn co http://llvm.org/svn/llvm-project/libcxxabi/branches/release_50/ libcxxabi`
* `mkdir -p libcxx/build libcxxabi/build`
* `cd /tmp/libcxx/build`
* `cmake -DCMAKE_BUILD_TYPE=Release -DLLVM_CONFIG_PATH=/usr/bin/llvm-config-5.0 -DCMAKE_INSTALL_PREFIX=/usr .. && make install`
* `cd /tmp/libcxxabi/build`
* `CPP_INCLUDE_PATHS=echo | c++ -Wp,-v -x c++ - -fsyntax-only 2>&1   |grep ' /usr'|tr '\n' ' '|tr -s ' ' |tr ' ' ';'`
* `CPP_INCLUDE_PATHS="/usr/include/c++/v1/;$CPP_INCLUDE_PATHS"`
* `cmake -G "Unix Makefiles" -DLIBCXX_CXX_ABI=libstdc++ -DLIBCXX_LIBSUPCXX_INCLUDE_PATHS="$CPP_INCLUDE_PATHS" -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr -DLLVM_CONFIG_PATH=/usr/bin/llvm-config-5.0       -DLIBCXXABI_LIBCXX_INCLUDES=../../libcxx/include .. && make install`
* `cd /tmp/libcxx/build`
* `cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr -DLIBCXX_CXX_ABI=libcxxabi -DLLVM_CONFIG_PATH=/usr/bin/llvm-config-5.0 -DLIBCXX_CXX_ABI_INCLUDE_PATHS=../../libcxxabi/include .. && make install`

### Install Qt

* `wget http://download.qt.io/official_releases/online_installers/qt-unified-linux-x64-online.run`
* Make executable & run from GUI

### Install QtCreator Beta

* https://download.qt.io/snapshots/qtcreator/4.6/4.6.0-beta1
* Download a version
* Make it executable & run from GUI

## Setup QtCreator

### Plugins

* Help - About Plugins…
  * enable in "C++"
    * `ClangCodeModel`
  * disable unused "Version Control"

### Clang Format

* Options / Beatifier - Tab: General
  * [x] Enable auto format on File Save
  * Tool: Select "ClangFormat"
* Select tab: "Clang Format"
  * Clang Format command: `/usr/lib/llvm-6.0/bin/clang-format`
  * Use predefined style: Select "File"
  * Fallback style: "None"

### Add Clang Compilers

* Options / Build & Run - Tab: Compilers - Add - Clang - C
  * Compiler path: `/usr/lib/llvm-5.0/bin/clang`
  * Compiler path: `/usr/lib/llvm-6.0/bin/clang`
* Options / Build & Run - Tab: Compilers - Add - Clang - C++
  * Compiler path: `/usr/lib/llvm-5.0/bin/clang++`
  * Compiler path: `/usr/lib/llvm-6.0/bin/clang++`
* Go to Tab: Kits - Add
  * Name: %{Compiler:Name} - %{Qt:Name}
  * Compiler: C: (Select Clang) C++: (Select Clang)
  * optionally Qt version: …
* Hit "Apply" button to save

### Add Qt

* Options / Build & Run - Tab: Qt Versions - Add…
  * Browse to path: `/opt/Qt/5.10.0/gcc_64/bin` - select `qmake`
  * Version name: `Qt %{Qt:Version} (gcc_64)`
  * qmake Location: `/opt/Qt/5.10.0/gcc_64/bin/qmake`
* Hit "Apply" button to save

### Add GCC kit

* Options / Build & Run - Tab: Kits - Add
  * Name: %{Compiler:Name} - %{Qt:Name}
  * Compiler: C: (Select GCC 5) C++: (Select GCC 5)
  * Qt version: `from last step`
* Hit "Apply" button to save
