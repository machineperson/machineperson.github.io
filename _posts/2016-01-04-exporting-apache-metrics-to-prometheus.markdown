---
layout: post
title:  "Exporting Apache metrics to Prometheus, for Go newbies"
date:   2016-01-04 17:27:08
categories: monitoring 
---


There are two kinds of tech companies: those that have monitoring, and those that have not had a major outage yet. I work for a startup that is in the second category, and since I'm the closest thing to a Site Reliability Engineer the company has, it's my job to make sure we have monitoring before disaster strikes.

Enter [Prometheus][prometheus], an open source monitoring system developed by ex-Google engineers. Since we're a SaaS company, it seemed like a good idea to start with monitoring our web servers. For Prometheus itself as well as basic system stats, there are [Debian packages][robust-perception-deb] put together by [Robust Perception][robust-perception] which came in quite handy. That's not quite enough if you're interested in how your actual web servers are doing though, so I was pleased to find out that there is an [apache-exporter][apache-exporter] module that'll export Apache stats for you. 

Prometheus and the Apache exporter module are both written in Go, and I'm more of a Python person lately and haven't really had a chance to do anything with Go yet. At the time I installed it, the instructions assumed that you had used the Go ecosystem before - "With working golang environment it can be built with `go get`". Full stop. Perfectly sufficient if you have a working golang environment and know what they are trying to tell you, but I needed a bit of googling to figure it out. So here's how I managed to set it up from scratch, on Ubuntu: 

## Set up golang environment

First, you need to install Go if you haven't yet:
```
$ sudo apt-get install golang
```

Go needs to know where your Go workspace is so it can install packages, and you need to set that manually. I just created a subdirectory in my home directory, but you can put this wherever you want. 

```
$ mkdir ~/go
```

`go get` needs you to set an environment variable called `GOPATH` to point to your workspace, so put this in your `.bashrc` or equivalent:

```
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

The `GOROOT` variable points to the location the Go tools are installed - if you didn't install them to a custom location, you don't have to set this manually. 

## Install 

Now you should be ready to install the Apache exporter:

```
$ go get github.com/neezgee/apache_exporter
```

## Run as a service 

You might want to run the module as a service, to make it easier to start and stop.

First create a soft link to the exporter in ``/usr/bin/``:

```
$ sudo ln -s ~/go/bin/apache_exporter /usr/bin
```

Then, create an Upstart configuration file named ``/etc/init/apache_exporter.conf`` with the following contents:

    start on startup

    script
        /usr/bin/apache_exporter
    end script


Now Apache Exporter is available as a service, and you can start it using 

```
$ sudo service apache_exporter start
```

## Add to Prometheus targets

Apache Exporter outputs to ``http://your_server_ip:9117/metrics`` by default (if you want to use a different port, you can set the `-telemetry.address string` flag to your desired port number when starting it). If you've installed and started it correctly, and Apache is running on your machine, you should now be able to see the metrics it exposes when you go to that address (it might take a minute or two after starting up).

Now all that's left is to tell your Prometheus server about it - you need to do that in the Prometheus config, as the individual exporters just expose their metrics and Prometheus pulls metrics from the targets it knows about.

Open your Prometheus config files, and add your machine to the ``scrape_configs`` section as follows:

    scrape_configs:
      - job_name: "apache"
        scrape_interval: "15s"
        target_groups:
        - targets: ['your_server_ip:9117']
    
Reload the Prometheus config (you can send a SIGHUP to the Prometheus process to reload it at runtime, or restart it), and you should now be able to access your Apache server's metrics through Prometheus!

[apache-exporter]: https://github.com/neezgee/apache_exporter
[prometheus]: http://prometheus.io/
[robust-perception-deb]: http://www.robustperception.io/machine-monitoring-with-prometheus-debs/
[robust-perception]: http://www.robustperception.io

