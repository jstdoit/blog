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
* all things goes fine, but the when i run `gem`, it alerts me with msg `It seems your ruby installation is missing psych (for YAML output). To eliminate this warning, please install libyaml and reinstall your ruby.`, ooooh! i never met such problem on Ubuntu...
I found that `rvm` gives me an option for install `libyaml` by running `rvm pkg install libyaml`, then `rvm reinstall 1.9.3`, but warning still exists.
:-( then I should abandon the RVM, and install ruby via src-code.
* first install `libyaml`
{% codeblock %}
$ wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz
$ tar xzvf yaml-0.1.4.tar.gz
$ cd yaml-0.1.4
$ ./configure --prefix=/usr/local
$ make
$ make install
{% endcodeblock %}
* then install `ruby`
{% codeblock lang:bash%}
$ wget http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p0.tar.gz
$ tar xzvf ruby-1.9.3-p0.tar.gz
$ cd ruby-1.9.3-p0
$ ./configure --prefix=/usr/local --enable-shared --disable-install-doc --with-opt-dir=/usr/local/lib
$ make
$ make install
{% endcodeblock %}
Finally, the world is clear!
