---
title: Installing bluehydra on raspberry pi
date: 2016-08-23 20:22
lang: en
---

## Introduction
ap
BlueHydra is a Bluetooth device discovery service built on top of the bluez library.
In this tutorial we are going to give the steps to install bluehydra on the rasperberry pi using Raspbian GNU/Linux 8 (jessie).
If you are using raspbien wheezy, please see the Apendix

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
 sudo apt-get install python-bluez python-dbus sqlite3 bluez-tools ruby-dev
{% endhighlight %}


## BlueHydra Instalation

{% highlight bash %}
 git clone https://github.com/pwnieexpress/blue_hydra.git
 cd blue_hydra
 bundle install
{% endhighlight %}


## Final Result

{% highlight bash %}
sudo ./bin/blue_hydra
{% endhighlight %}


If everything is right, you should see the bluehydra working.


![alt text](https://github.com/llazzaro/llazzaro.github.io/blob/master/_posts/blue_hydra.png?raw=true "Blue_hydra on raspbian jessie")

if you have a python import error on bluezutils, try to download bluezutils.py and copy it to the bin directory of blue hydra:

{% highlight bash %}
 wget https://raw.githubusercontent.com/pauloborges/bluez/master/test/bluezutils.py
{% endhighlight %}


# Apendix 

## Not working in wheezy
When I began writting this tutorial I was using wheezy, but since I got a lot of issues with dbus and errors like:

{% highlight bash %}
E, [2016-08-24T00:19:43.606500 #27471] ERROR -- :   File "/home/pi/blue_hydra/bin/test-discovery", line 15, in <module>
E, [2016-08-24T00:19:43.606813 #27471] ERROR -- :     import bluezutils
{% endhighlight %}

{% highlight bash %}
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.UnknownMethod: Method "GetManagedObjects" with signature "" on interface "org.freedesktop.DBus.ObjectManager" doesn't exist
{% endhighlight %}

To check which version you are using just cat /etc/os-release

