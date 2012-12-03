---
layout: post
title: "Install Openfire on Ubuntu 12.04 Server"
date: 2012-11-30 10:35
comments: true
categories: [Openfire, Server, XMPP]
---
Wow! The vines xmpp server annoyed me so much. it's not implemented well, and not tested for concurrent. So i abandon it and changed to Openfire.
when I install Openfire on My ubuntu 12.04 server, met the problem about jre, cos java is bought by Oracle and change some copyright doings, so we cannot find Sun's jre on Ubuntu via `apt-get` tools, after searching, i found the tool offered by `github` [oab-java6](https://github.com/flexiondotorg/oab-java6) can easily solve the problem of Installing the sun's jre on ubuntu. steps as follows:
<!-- more -->
* `wget https://raw.github.com/flexiondotorg/oab-java6/master/oab-java.sh`
* `chmod +x oab-java.sh`
* `./oab-java.sh`
* then we can install sun's jre by executing `sudo apt-get install sun-java6-jre`
* After install jre, get [Openfire](http://www.igniterealtime.org/projects/openfire/index.jsp) from [here](http://www.igniterealtime.org/downloads/index.jsp)(Ubuntu's deb version)
* `dpkg -ivh openfire_3.7.1_all.deb`
the configuration of Openfire can follow [here](http://www.igniterealtime.org/builds/openfire/docs/latest/documentation/install-guide.html)
