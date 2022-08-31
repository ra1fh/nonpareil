.. image:: https://github.com/ra1fh/nonpareil/actions/workflows/build.yml/badge.svg
    :target: https://github.com/ra1fh/nonpareil/actions/workflows/build.yml

Nonpareil Simulator for Electronic Calculators
==============================================

.. image:: doc/screenshot.png
   :width: 350
   :align: center

Nonpareil is a microcode-level simulator for electronic calculators
written by Eric Smith and published at
`https://nonpareil.brouhaha.com/ <https://nonpareil.brouhaha.com/>`_

This repository contains some modifications compared to nonpareil-0.79:

* The build system has been replaced with CMake 3
* Some correctness and compilation fixes
* ROMs are not included and will be downloaded during the build.

Currently it is able to simulate some of the HP calculators introduced
between 1972 and 1982:

* HP-35, HP-45, HP-55, HP-80
* HP-21, HP-25,
* HP-31E, HP-33C, HP-34C, HP-37E, HP-38E, HP-38C
* HP-41CV, HP-41CX

Build Requirements
------------------

* CMake >= 3.11
* GTK+-2
* SDL 1.2
* bison >= 3.0
* flex
* libpng
* libxml2

Tested Operating Systems
------------------------

* Debian GNU/Linux
* Fedora
* OpenBSD (amd64, macppc, sparc64)
* Raspberry Pi OS (32-bit and 64-bit)
* Ubuntu (20.04 and 22.04)
* Windows

Installing Build Dependencies
-----------------------------
  
Debian, Raspberry Pi OS, Ubuntu
```````````````````````````````
::

   sudo apt install bison cmake flex git libfl-dev \
        libgtk2.0-dev libpng-dev libsdl1.2-dev libxml2-dev

Fedora
``````
::

   sudo dnf install bison cmake flex git gtk2-devel \
        libfl-devel libpng-devel libxml2-devel sdl12-compat-devel

OpenBSD
```````
::

   doas pkg_add bison cmake git gtk+2 libxml png sdl

Windows
```````

Windows 11 Build 22000 or later has X11/Wayland support in Windows
Subsystem for Linux (WSL).  `Setup WSL
<https://docs.microsoft.com/en-us/windows/wsl/tutorials/gui-apps>`_,
install an Ubuntu container and follow the Ubuntu instructions.

Compilation and Installation
----------------------------

::

   git clone https://github.com/ra1fh/nonpareil.git
   mkdir -p nonpareil-build
   cd nonpareil-build
   cmake ../nonpareil
   make
   sudo make install

Usage
-----

* Invoke ``nonpareil`` from the command line and select a calculator
  model by loading a KML file
* Or: use one of the alias commands that preselect a KML file: 21,
  25, 32e, 33c, 34c, 35, 37e, 38c, 38e, 41cv, 41cx, 45, 55, 80


::

   usage: nonpareil [options...] kmlfile
   options:
      --shape (default)
      --noshape
      --kmldebug
      --kmldump
      --scancodedebug
      --nostate
      --trace
      --stop
