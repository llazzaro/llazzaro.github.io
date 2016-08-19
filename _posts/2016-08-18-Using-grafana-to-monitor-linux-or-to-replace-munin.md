---
title: Monitor linux statistics with grafana (munin replacement)
date: 2016-08-18 23:42
lang: en
---

This quick tutorial will guide you to install grafana to monitor a linux system.
We also are going to save a history to an influx database.

# Installing InfluxDB

For Ubuntu

{% highlight bash %}
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
{% endhighlight %}

Install and start theInfluxDB service:

{% highlight bash %}
sudo apt-get update && sudo apt-get install influxdb
sudo service influxdb start
{% endhighlight %}

Now you can check at the port 8083 (for a local installation check http://localhost:3000)) that the InfluxDB is working.

SCREENSHOT

Since InfluxDB default config is a little insecure let's change some settings:


sudo vim /etc/influxdb/influxdb.conf

Now search for *auth-enabled* and set it to true.

it's recommended to enable also *https-enabled*

using the influx-cli create the admin user (remeber to start the service with influxd):

influx
CREATE USER leonardo WITH PASSWORD 'use_secure_password' WITH ALL PRIVILEGES


# Installing Grafana
We need to download the official .deb file (please check the updated .deb here http://grafana.org/download/)

{% highlight bash %}
wget https://grafanarel.s3.amazonaws.com/builds/grafana_3.1.1-1470047149_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_3.1.1-1470047149_amd64.deb
sudo service grafana-server start
{% endhighlight %}

Now check that Grafana web interface is working on localhost at port 3000 (for a local installation check http://localhost:3000).

SCREENSHOT

Sign up with a new user and then open the grafana configuration (/etc/grafana/grafana.ini) to uncomment *admin_user* and *admin_password*

{% highlight bash %}
admin_user = new_user
admin_password = password
{% endhighlight %}

Then restart grafana

{% highlight bash %}
sudo service grafana-server start
{% endhighlight %}

Configure InfluxDB data source. Since telegraf database is not yet created an error is expected.
Add the datasource as the following screenshot shows.

SCREENSHOT

# Collect and metrics reporting

Now we need a Grafana pluging to collect and report metrics, in this case we are going to use telegraf (https://github.com/influxdata/telegraf)

{% highlight bash %}
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.0.0-beta3_amd64.deb
sudo dpkg -i telegraf_1.0.0-beta3_amd64.deb
{% endhighlight %}

Next step is to configure the pluging. Telegraf can generate a file with specific inputs and outputs, you can use the -input-filter and -output-filter flags

{% highlight bash %}
telegraf -sample-config -input-filter cpu:mem:net:swap -output-filter influxdb:kafka > telegraf.conf
{% endhighlight %}

The last command will create a config to monitor CPU, memory, network and swap.

Now we need to add telegraf as a service

COMPLETE
