---
title: Installing bluehydra on raspeberrypi
date: 2016-08-23 20:22
lang: en
---

# Introduction

BlueHydra is a Bluetooth device discovery service built on top of the bluez library.
In this tutorial we are going to give the steps to install bluehydra on the rasperberry pi using Raspbian GNU/Linux 8 (jessie).
When I began writting this tutorial I was using wheezy, but since I got a lot of issues with dbus and errors like:

{% highlight bash %}
E, [2016-08-24T00:19:43.606500 #27471] ERROR -- :   File "/home/pi/blue_hydra/bin/test-discovery", line 15, in <module>
E, [2016-08-24T00:19:43.606813 #27471] ERROR -- :     import bluezutils
{% endhighlight %}

{% highlight bash %}
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.UnknownMethod: Method "GetManagedObjects" with signature "" on interface "org.freedesktop.DBus.ObjectManager" doesn't exist
{% endhighlight %}

To check which version you are using just cat /etc/os-release

## Installing ruby with rvm

There are several ways to install ruby, the one I prefer is with rvm.
rvm is  is a command-line tool which allows you to easily install, manage, and work with multiple ruby environments.
Download and install rvm with:

{% highlight bash %}
curl -L https://get.rvm.io | bash -s stable --ruby
{% endhighlight %}

This will probably raise the error:

{% highlight bash %}
gpg: Can't check signature: public key not found
{% endhighlight %}

Read the instructions to download the signatures from the error..

Now let's check that we are using a correct ruby version:

{% highlight bash %}
 rvm current
{% endhighlight %}

In the following steps we are going to use blunder to install ruby gems.

{% highlight bash %}
    sudo gem install bundler
{% endhighlight %}

Now that we have ruby working on the raspberry pi, we are going to install the dependencies.

{% highlight bash %}
sudo apt-get install bluez-utils python-bluez python-dbus sqlite3 bluez-tools bluez-utils
{% endhighlight %}

If for some reason python bluezutils is throwing ImportError like:

{% highlight bash %}
E, [2016-08-24T00:19:43.606500 #27471] ERROR -- :   File "/home/pi/blue_hydra/bin/test-discovery", line 15, in <module>
E, [2016-08-24T00:19:43.606813 #27471] ERROR -- :     import bluezutils
{% endhighlight %}

To avoid that error I downloaded the bluezutils from github () and added to the bind directory of blue_hydra (do it in the next steps).


## BlueHydra Instalation

{% highlight bash %}
    git clone https://github.com/pwnieexpress/blue_hydra.git
    cd blue_hydra
    bundle install
{% endhighlight %}

At this point I started to have this error:
o fix it, download and install bluez 5.41

{% highlight bash %}
sudo apt-get update
sudo apt-get install libdbus-1-dev libdbus-glib-1-dev libglib2.0-dev libical-dev libreadline-dev libudev-dev libusb-dev make
wget https://www.kernel.org/pub/linux/blueto ... 5.4.tar.xz
xz -d bluez-5.4.tar.xz
tar xvf bluez-5.4.tar
cd bluez-5.4
./configure --disable-systemd
make
sudo make install
{% endhighlight %}

## Final Result

{% highlight bash %}
sudo ./bin/blue_hydra
{% endhighlight %}

If everything is right, you should see the bluehydra working.


![alt text](https://raw.githubusercontent.com/llazzaro/llazzaro.github.io/master/_posts/Screen%20Shot%202016-06-28%20at%2012.00.16%20AM.png "Logo Title Text 1")


