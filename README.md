# mad-ady/smokeping - based on magicdud4eva/smokeping

Smokeping keeps track of your network latency. For a full example of what this application is capable of visit [UCDavis](http://smokeping.ucdavis.edu/cgi-bin/smokeping.fcgi). The Smokeping Docker image includes the latest version of Smokeping, speedtest-cli and PhantomJS.

![Smokeping Docker](https://github.com/magicdude4eva/docker-smokeping/raw/master/docker-smokeping.png)

## TL;DR - Features
* Private Smokeping branch (https://github.com/mad-ady/SmokePing)
* Speedtest probe (https://github.com/mad-ady/smokeping-speedtest / https://github.com/sivel/speedtest-cli)
* Youtube-dl probe (https://github.com/mad-ady/smokeping-youtube-dl)
* PhantomJS (http://phantomjs.org/)
* Working configuration for DNS, Speedtest and web-site probes
* Ability to run as a slave

[![smokeping](http://oss.oetiker.ch/smokeping/inc/smokeping-logo.png)][smokeurl]
[smokeurl]: http://oss.oetiker.ch/smokeping/

## Usage
Run standalone:

```
docker create \
    --name smokeping \
    -p 9500:80 \
    -e PUID=<UID> -e PGID=<GID> \
    -e TZ=<timezone> \
    -v <path/to/smokeping/data>:/data \
    -v <path/to/smokeping/config>:/config \
    madady/smokeping
```

Run as a slave:
```
docker create \
    --name smokeping \
    -e PUID=<UID> -e PGID=<GID> \
    -e TZ=<timezone> \
    -e MASTERURL=<http://user:pass@master.server.url/smokeping/smokeping.fcgi>
    -v <path/to/smokeping/data>:/data \
    -v <path/to/smokeping/config>:/config \
    -v <path/to/secrets>:/config/secret \
    madady/smokeping
```

## Parameters

`The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side. 
For example with a port -p external:internal - what this shows is the port mapping from internal to external of the container.
So -p 9500:80 would expose port 80 from inside the container to be accessible from the host's IP on port 9500
http://192.168.x.x:9500 would show you what's running INSIDE the container on port 80.`


* `-p 80` - the port for the webUI
* `-v /data` - Storage location for db and application data (graphs etc)
* `-v /config` - Configure the `Targets` file here
* `-v /config/secret` - File holding the slave secret (used when running as a slave)
* `-e PGID` for for GroupID - see below for explanation
* `-e PUID` for for UserID - see below for explanation
* `-e TZ` for timezone setting, eg Africa/Johannesburg
* `-e MASTERURL` for a URL to the master instance (causes this container to be a slave)


This container is based on phusion/baseimage and includes the latest build of PhantomJS. For shell access whilst the container is running do `docker exec -it smokeping /bin/bash`.

### User / Group Identifiers
Sometimes when using data volumes (`-v` flags) permissions issues can arise between the host OS and the container. We avoid this issue by allowing you to specify the user `PUID` and group `PGID`. Ensure the data volume directory on the host is owned by the same user you specify and it will "just work" <sup>TM</sup>.

In this instance `PUID=1001` and `PGID=1001`. To find yours use `id user` as below:

```
  $ id dockeruser
    uid=1001(dockeruser) gid=1001(dockergroup) groups=1001(dockergroup)
```

## Setting up the application 

Once running the URL will be `http://<host-ip>:9500/smokeping/smokeping.cgi`.

Basics are, edit the Targets file to ping the hosts you're interested in to match the format found there. 
Wait 10 minutes.

## Info

* To monitor the logs of the container in realtime `docker logs -f smokeping`.

