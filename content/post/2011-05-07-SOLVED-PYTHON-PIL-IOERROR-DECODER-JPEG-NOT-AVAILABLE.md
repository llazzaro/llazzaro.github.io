+++
title = "PYTHON PIL IOERROR DECODER JPEG NOT AVAILABLE"
date = "2011-05-07"
lang = "en"
+++

The first thing I check when I got this error was to check if libjpeg was installed.

Lets try this

{{< highlight bash >}}
sudo apt-get install libjpeg libjpeg-dev
sudo apt-get install libfreetype6 libfreetype6-dev
download jpeg source
{{< / highlight >}}


{{< highlight bash >}}
    tar -xzvf jpegsrc.v8c.tar.gz
    cd jpeg-6b/
    ./configure
    make
    sudo make install
{{< / highlight >}}
For zlib support (png/zip)

{{< highlight bash >}}
    sudo ln -s /usr/lib/x86_64-linux-gnu/libz.so /usr/lib
{{< / highlight >}}
So download Python Imaging Library 1.1.7 Source Kit (all platforms)

The after run the setup.py install, check if the support was ok

{{< highlight bash >}}
--------------------------------------------------------------------
*** TKINTER support not available (Tcl/Tk 8.5 libraries needed)
--- JPEG support available
--- ZLIB (PNG/ZIP) support available
*** FREETYPE2 support not available
--- LITTLECMS support available
--------------------------------------------------------------------
{{< / highlight >}}

If not, in setup.py in PIL I had to change the path of :

{{< highlight python >}}
JPEG_ROOT = None
ZLIB_ROOT = None
{{< / highlight >}}
to

{{< highlight python >}}
JPEG_ROOT = libinclude("/usr/")
ZLIB_ROOT = libinclude("/usr/")
{{< / highlight >}}