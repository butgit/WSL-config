# WSL as a Development Environment 
This is my modified version of hsab's instruction on setting up a vim as IDE in wsl environment. 

The following is a tutorial for setting up [Windows Subsystem for Linux](https://msdn.microsoft.com/en-us/commandline/wsl/about) as a development environment for Python, Python 3, Node, Ruby, C, C++, Objective-C, Objective-C++, Javascript etc.

The core components are [Zsh](http://www.zsh.org/) and [Vim](http://www.vim.org/). Vim with other tools such as [tmux](https://tmux.github.io/) is a powerful development tool.  

For the Zsh setup, I will be using [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) and [antigen](https://github.com/zsh-users/antigen) as my bundle manager. 

We will be removing the default Vim package and compile our own from source to use plugins such as [YouCompleteMe](https://github.com/Valloric/YouCompleteMe) and [Command-T](https://github.com/wincent/command-t).

This guide assumes that you have just completed a [fresh installation](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) of WSL. 

This repository provides starting `.vimrc` and `.zshrc` config files. Everything is commented for clarity. Please research each plugin to see its functionality and use.

First thing first make sure you have `git` on your WSL install and clone this repository in your `~` directory:

    cd 
    git clone https://github.com/hsab/WSL-config.git

Rename the folder to make it hidden:

    mv ./WSL-config ./.wslconfig 

Now update and upgrade everything to make sure all of our installed packages are up-to-date.

    sudo apt-get update
    sudo apt-get upgrade

Console Info:
=================
By the end of **Zsh Installation** section your terminal should look like this:

![enter image description here](http://i.imgur.com/ALqC0Ad.png)

~~In this screenshot I'm using [cmder](http://cmder.net/) with [wsltty](https://github.com/mintty/wsltty) using `london-tube` color scheme.

~~If you decide to use the same console setup, you can copy `mintty` folder to `%appdata%` to have access to the themes. I have also included my `ConEmu.xml` config file if you decide to use it.

~~For correct encoding of special characters, you need to install [patched fonts for Powerline](https://github.com/powerline/fonts). Make sure to install these fonts as you would normally on Windows. Even though `./install.sh` is provided there is no point in running this script as the GUI is provided through the Windows OS.

I'm not big on a fancy terminal and a bunch of powerline fonts. I simply opted for Windows Terminal (Preview) and Cascadia Code for powerline. Just install [cascadia code](https://github.com/microsoft/cascadia-code/releases) and add "fontFace":  "Cascadia Code PL" in the setting.json file of Windows Terminal.

Zsh Installation:
=================
By the end of this section we will have installed [zsh](http://www.zsh.org/), [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) and [antigen](https://github.com/zsh-users/antigen) as well as creating a symbolic link for our `.zshrc` file. Install Zsh first:

    sudo apt-get install zsh

Installing oh-my-zsh:
------------------------
[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) is an open source, community-driven framework for managing your Zsh configuration.
Let's install oh-my-zsh using `curl`:

    cd
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

Create Symbolic .zshrc:
-------
If you want to use the provided `.zshrc` file in the repository create a symbolic link in your `~` directory:

    ln -s ~/.wslconfig/.zshrc  ~/.zshrc
This didn't work for me. (File already exists.) Instead, I copy-pasted. 

Make sure to read up on the installed bundles for Zsh in this `.zshrc` file. There are some interesting features e.g fuzzy command history mapped to `Ctrl+R` .

Installing antigen:
------------------------
[antigen](https://github.com/zsh-users/antigen) is a small set of functions that help you easily manage your Zsh plugins, called bundles. The concept is pretty much the same as bundles in a typical vim setup. Antigen is to zsh, what Vundle is to vim.

Let's install antigen using `curl`:

    cd
    curl -L git.io/antigen > antigen.zsh
    mkdir .antigen
    mv antigen.zsh ./.antigen/


Set Zsh as Default Shell:
-------------------------
In order to set Zsh as our default shell add the following snippet to you `.bashrc` file located in `~` directory:
(I didn't need this because zsh set itself as the default shell.)

    # Launch Zsh
    if [ -t 1 ]; then
    exec zsh
    fi

Finally restart your terminal and wait for antigen to install all plugins. 
*If you encounter "zsh compinit: insecure directories, run compaudit for list" press [n] and run*

    cd ~
    compaudit | xargs chown -R "$(whoami)"
    compaudit | xargs chmod -R go-w

Vim Installation:
=================
By the end of this section we will have compiled and installed [Vim](https://github.com/vim/vim) from source, installed all of our development dependencies, as well as our Vim plugins.
![enter image description here](http://i.imgur.com/4mRiFMi.png)

Installing Build Essentials & Cmake:
------------------------
The build-essentials is a reference for all the packages needed to compile a debian package. It generally includes the `gcc/g++` compilers an libraries and some other utils. 

CMake is an extensible, open-source system that manages the build process in an operating system and in a compiler-independent manner. 

    sudo apt install build-essential cmake curl


Installing Node, NPM & Typescript:
--------------
Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.

THIS IS OUTDATED

    curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
    sudo apt-get install -y nodejs

TypeScript is a free and open-source programming language developed and maintained by Microsoft. It is a strict superset of JavaScript, and adds optional static typing and class-based object-oriented programming to the language.
    
    npm install -g typescript

Remove Vim:
--------------
Remove Vim from your WSL setup:

    sudo apt remove vim vim-runtime gvim

Just to be safe remove these packages as well:

    sudo apt remove vim-tiny vim-common vim-gui-common vim-nox

Installing Dependencies for Vim & Ruby:
--------------
Compiling Vim from source is actually not that difficult. Install all the prerequisite libraries: 

Required for vim:

    sudo apt install libncurses5-dev libgnome2-dev libgnomeui-dev \
    libgtk2.0-dev libatk1.0-dev libbonoboui2-dev \
    libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev \
    python3-dev ruby-dev lua5.1 liblua5.1-dev libperl-dev git

python-dev is development package for Python 2. It is required for compilling some bundles. 

Required for RVM:

    sudo apt install bison libffi-dev libgdbm-dev libsqlite3-dev libyaml-dev sqlite3 libssl-dev


Installing RVM and Ruby:
--------------
Ruby is a dynamic, open source programming language with a focus on simplicity and productivity. It is required for the `Command-T` plugin.

You can check if you have Ruby installed by doing `ruby -v` and `rvm -v`. 
Don't forget the last line. VERSION is the number you get from the command 'ruby -v'.
If you get a message saying *The program 'ruby' can be found in the following packages*, you need to install Ruby and rvm.

    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
    curl -L https://get.rvm.io | bash -s stable --ruby
    rvm reinstall VERSION -C --enable-shared
    
Set .zshrc for rvm scripting:

    source "$HOME/.rvm/scripts/rvm"


Installing Clang:
--------------
Clang is an open-source compiler that can compile C/C++/Objective-C/Objective-C++. The `libclang` library it provides is used to power the YCM semantic completion engine for those languages.

The following is originally written in this post: 

    sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-VERSION  main"
    sudo apt-get update
    sudo apt-get install clang-VERSION  lldb-VERSION 

However, I followed this:
    
    sudo apt install xz-utils
    curl -SL http://releases.llvm.org/9.0.0/clang+llvm-9.0.0-x86_64-linux-gnu-ubuntu-18.04.tar.xz | tar -xJC .
    mv clang+llvm-9.0.0-x86_64-linux-gnu-ubuntu-18.04 clang_9.0.0
    sudo mv clang_9.0.0 /usr/local

Add these to .bashrc or .zshrc:

    export PATH=/usr/local/clang_9.0.0/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/clang_9.0.0/lib:$LD_LIBRARY_PATH

Installing Checkinstall:
--------------
checkinstall is a simple program which monitors the installation of files, and creates a Debian package from them. There are two primary benefits to using checkinstall instead of running make install: You can easily remove the package with one step. You can install the resulting package upon multiple machines.

If you want to be able to easily uninstall vim use `checkinstall`.

    sudo apt-get install checkinstall



Compiling Vim:
--------------
Once everything is installed, getting the source is easy. 
**Note**: If you are using Python, your config directory might have a machine-specific name (e.g. config-3.5m-x86_64-linux-gnu). Check in `/usr/lib/python[2/3/3.5]` to find yours, and change the `python-config-dir` and/or `python3-config-dir` arguments accordingly.


    cd ~
    git clone https://github.com/vim/vim.git
    cd vim
    ./configure --with-features=huge \
                --enable-multibyte \
                --enable-rubyinterp=yes \
                --enable-python3interp=yes \
                --with-python3-config-dir=$(python3-config --configdir) \
                --enable-perlinterp=yes \
                --enable-luainterp=yes \
                --enable-gui=gtk2 \
                --enable-cscope \
                --prefix=/usr/local

Make Vim

    make VIMRUNTIMEDIR=/usr/local/share/vim/vim82

And install:

    cd ~/vim
    sudo checkinstall

[1^]

Setup Vundle:
--------------
Vundle is a plugin manager for Vim. Clone the [repository](https://github.com/VundleVim/Vundle.vim) into the appropriate `.vim` bundle folder.

    git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim



Vanilla .vimrc:
---------------
Put this at the top of your `.vimrc` to use [Vundle](https://github.com/VundleVim/Vundle.vim). Remove plugins you don't need, they are for illustration purposes.

    set nocompatible              " be iMproved, required
    filetype off                  " required
    
    " set the runtime path to include Vundle and initialize
    set rtp+=~/.vim/bundle/Vundle.vim
    call vundle#begin()
    
    " let Vundle manage Vundle, required
    Plugin 'VundleVim/Vundle.vim'
    
    " The following are examples of different formats supported.
    " Keep Plugin commands between vundle#begin/end.
    " plugin on GitHub repo
    " Plugin 'tpope/vim-fugitive'
    " plugin from http://vim-scripts.org/vim/scripts.html
    " Plugin 'L9'
    " Git plugin not hosted on GitHub
    " Plugin 'git://git.wincent.com/command-t.git'
    " git repos on your local machine (i.e. when working on your own plugin)
    " Plugin 'file:///home/gmarik/path/to/plugin'
    " The sparkup vim script is in a subdirectory of this repo called vim.
    " Pass the path to set the runtimepath properly.
    " Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
    " Install L9 and avoid a Naming conflict if you've already installed a
    " different version somewhere else.
    " Plugin 'ascenator/L9', {'name': 'newL9'}
    
    " All of your Plugins must be added before the following line
    call vundle#end()            " required
    filetype plugin indent on    " required
    " To ignore plugin indent changes, instead use:
    "filetype plugin on
    "
    " Brief help
    " :PluginList       - lists configured plugins
    " :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
    " :PluginSearch foo - searches for foo; append `!` to refresh local cache
    " :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
    "
    " see :h vundle for more details or wiki for FAQ
    " Put your non-Plugin stuff after this line

Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

Installing YouCompleteMe:
-------------------------
YouCompleteMe is a fast, as-you-type, fuzzy-search code completion engine for Vim. It has several completion engines:

 - an identifier-based engine that works with every programming language,
 - a Clang-based engine that provides native semantic code completion for C/C++/Objective-C/Objective-C++ (from now on referred to as "the C-family languages"),
 - a Jedi-based completion engine for Python 2 and 3 (using the JediHTTP wrapper),
 - an OmniSharp-based completion engine for C#,
 - a combination of Gocode and Godef semantic engines for Go,
 - a TSServer-based completion engine for TypeScript,
 - a Tern-based completion engine for JavaScript,
 - a racer-based completion engine for Rust,
 - and an omnifunc-based completer that uses data from Vim's omnicomplete system to provide semantic completions for many other languages (Ruby, PHP etc.).

Add the following line to your `.vimrc` file:

    Plugin 'Valloric/YouCompleteMe'

Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

`install.py` is the quickest way to install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe).

 - C# support: install Mono and add `--omnisharp-completer` when calling `./install.py`.
 - Go support: install Go and add `--gocode-completer` when
   calling `./install.py`.
 - TypeScript support: install Node.js and npm then install the TypeScript SDK with `npm install -g typescript`.
 - JavaScript support: install Node.js and npm and add `--tern-completer` when calling `./install.py`
 - Rust support: install Rust and add `--racer-completer` when calling `./install.py`.

Finally configure YCM:

    cd ~/.vim/bundle/YouCompleteMe 
    ./install.py --clang-completer --tern-completer

Installing Command-T:
---------------------
Command-T is a Vim plug-in that provides an extremely fast "fuzzy" mechanism for: Opening files and buffers, Jumping to tags and help
Running commands, or previous searches and commands with a minimal number of keystrokes.

Add the following line to your `.vimrc` file:

    Plugin 'wincent/command-t'

Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

Navigate to your Command-T bundle directory

    cd ~/.vim/bundle/command-t

Now that you are in your Command-T bundle directory. Do the following:

    rake make

That should recompile Command-T. 

Easy Debugging with Conque GDB:
-----------------------------------
[Conque GDB](http://www.vim.org/scripts/script.php?script_id=4582) turns Vim buffers into terminal emulators, which enables you to run CLI programs from inside Vim windows. 

Although you can run any CLI program with Conque GDB. The main reason for this plugin is to offer a nice and complete GDB command line interface inside of Vim. See [demonstration video](http://www.youtube.com/watch?v=ZcSjaYSoqnc&feature=youtu.be). 

Add the following line to your `.vimrc` file:

    Plugin 'vim-scripts/Conque-GDB'

Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

Check the [documentation](https://github.com/vim-scripts/Conque-GDB/blob/master/doc/conque_gdb.txt) for Conque GDB to see how you can easily toggle breakpoints and debug you apps with Vim's text editor.

Installing Vim Plugins:
-----------------------
Every time you add a plugin(s) to your `.vimrc` file you need to install the plugins(s) (duh). 
Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

Setting Vim as Default Editor:
------------------------------

Set Vim as your default editor with `update-alternatives`.

    sudo update-alternatives --install /usr/bin/editor editor /usr/local/bin/vim 1
    sudo update-alternatives --set editor /usr/local/bin/vim
    sudo update-alternatives --install /usr/bin/vi vi /usr/local/bin/vim 1
    sudo update-alternatives --set vi /usr/local/bin/vim

Create Symbolic .vimrc:
-------
If you want to use the provided `.vimrc` file in the repository create a symbolic link in your `~` directory:

    ln -s ~/.wslconfig/.vimrc ~/.vimrc
Launch Vim and run `:PluginInstall`
To install from command line: `vim +PluginInstall +qall`

Make sure to read up on the installed bundles for Vim in this `.vimrc` file.



Resources:
----------
Below is a list of original resources used to compile the steps above.

 - [Compile Vim from Source](https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source)
 ~~- [Install Vim with Ruby Support and Command-t Plugin on Ubuntu](http://www.justinbar.net/post/install-vim-with-ruby-support-and-command-t-plugin-on-ubuntu)~~ dead link
 - [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)
 - [YouCompleteMe Installation](https://github.com/Valloric/YouCompleteMe#ubuntu-linux-x64)
 - [Vundle Installation](https://github.com/VundleVim/Vundle.vim#quick-start)
 - [Command-T Docs](https://github.com/wincent/command-t/blob/master/doc/command-t.txt)
 - [Clang installation](https://solarianprogrammer.com/2017/12/13/linux-wsl-install-clang-libcpp-compile-cpp-17-programs/)
 - 

Footnotes:
----------
[^1]: You can remove it from your system anytime using:

    dpkg -r vim

