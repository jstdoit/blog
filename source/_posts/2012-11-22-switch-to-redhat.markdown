---
layout: post
title: "Switch to RedHat 5.4"
date: 2012-11-22 15:45
comments: true
categories: [Server]
---
these days, my boss asked me to transfer all our application to a server backed by RedHat 5.4 from Ubuntu, what a mass!
these is no `apt` tools anymore on the new server, i just cannot adapt my life to next days without `apt` tools. Whatever, i need to install these software:
`git`, `nodejs`, `ruby`, `nginx with passenger` for rails' deploy...
<!-- more -->

* all things goes fine, but the rails gem, it alerts me with msg `It seems your ruby installation is missing psych (for YAML output). To eliminate this warning, please install libyaml and reinstall your ruby.`, ooooh! i never met such problem on Ubuntu... after search on Google, i found the solutions as follows:
{% codeblock lang:bash%}
$ wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p0.tar.gz
$ tar xzvf ruby-1.9.3-p0.tar.gz
$ cd ruby-1.9.3-p0
$ ./configure --prefix=/usr/local --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
$ make
$ make install
{% endcodeblock %}
After `rvm reinstall 1.9.3`, the problem still exists. it seems that something goes wrong, then i checked the `rvm --help`, and found the option `-C`, so I add the preinstall `libyaml`'s libpath with `-C`, and execute `rvm -C --with-opt-dir=/usr/local/lib reinstall 1.9.3`.
But it still not work...
:-( i should abandon the RVM, and decide to install ruby via src-code.
{% codeblock lang:bash %}
http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p327.tar.gz`
tar vxzf ruby-1.9.3-p327.tar.gz
./configure --prefix=/usr/local --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
make
make install
{% endcodeblock %}
Finally, the world is clear!
