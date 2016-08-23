---
title: Remote disk encryption unlock via ssh
date: 2016-06-04 14:47
lang: en
---

The following steps were done with Debian Jessie.

This post is to configure a server unlocking which uses LUKS with LVM.
Usually servers are remote or does not have any monitor or any physical way to enter the key to unlock the disk.
The following steps are to setup a dropbear ssh service on boot, so the user can log in and enter the disk key via ssh.

Configuration

{% highlight bash %}
sudo apt-get install openssh-server dropbear busybox
{% endhighlight %}

{% highlight bash %}
sudo vi /etc/default/dropbear
{% endhighlight %}

{% highlight bash %}
update-initramfs -u
{% endhighlight %}

Create your ssh keys for the computer that will log in to unlock the disk if you don't did it before.
Execute this on the client machine:
{% highlight bash %}
ssh-keygen
{% endhighlight %}

now copy via scp your public key and append it to the dropbear home.

{% highlight bash %}
cat id_rsa.pub >> /etc/initramfs-tools/root/.ssh/authorized_keys
{% endhighlight %}

Remote unlocking manual steps

ssh to the server and execute the following steps:

{% highlight bash %}
/scripts/local-top/cryptroot
/sbin/vgchange -a y lv00
{% endhighlight %}

Now we have to kill cryptroot
{% highlight bash %}
ps | grep "/scripts/local-top/cryptroot" | cut -d " " -f 3 | xargs kill -9
exit
{% endhighlight %}

Now that we understand how to manually unlock the disk we can automate it with the following script:


References:
http://www.emmolution.org/?p=307
https://gist.github.com/wolli/5295625
https://help.ubuntu.com/community/SSH/OpenSSH/Keys
http://blog.nguyenvq.com/blog/2011/09/13/remote-unlocking-luks-encrypted-lvm-using-dropbear-ssh-in-ubuntu/
https://stinkyparkia.wordpress.com/2014/10/14/remote-unlocking-luks-encrypted-lvm-using-dropbear-ssh-in-ubuntu-server-14-04-1-with-static-ipst/
