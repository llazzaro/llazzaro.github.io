---
title: [SOLVED] PYTHON PIL : IOERROR: DECODER JPEG NOT AVAILABLE
date: 2011-05-07 21:11
lang: en
---

The first thing I check when I got this error was to check if libjpeg was installed.

Lets try this

sudo apt-get install libjpeg libjpeg-dev
sudo apt-get install libfreetype6 libfreetype6-dev
download jpeg source

    tar -xzvf jpegsrc.v8c.tar.gz
    cd jpeg-6b/
    ./configure
    make
    sudo make install
For zlib support (png/zip)

    sudo ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib
So download Python Imaging Library 1.1.7 Source Kit (all platforms)

The after run the setup.py install, check if the support was ok

--------------------------------------------------------------------
*** TKINTER support not available (Tcl/Tk 8.5 libraries needed)
--- JPEG support available
--- ZLIB (PNG/ZIP) support available
*** FREETYPE2 support not available
--- LITTLECMS support available
--------------------------------------------------------------------

If not, in setup.py in PIL I had to change the path of :

JPEG_ROOT = None
ZLIB_ROOT = None
to

JPEG_ROOT = libinclude("/usr/")
ZLIB_ROOT = libinclude("/usr/")
