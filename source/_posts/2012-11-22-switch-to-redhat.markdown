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
***
Next to install rmagick, when i execute following command: `yum install ImageMagick-devel ImageMagick-c++-devel`, my system alerts me with msg `Warning: Found a partial ImageMagick installation. Your operating system likely has some built-in ImageMagick libraries but not all of ImageMagick. This will most likely cause problems at both compile and runtime.` it seems that the the `ImageMagick` version was to old on RedHat 5.4, after searching the web, i found [Solve](https://github.com/hammackj/risu/issues/55), and commands as follows:
{% codeblock lang:bash %}
yum remove ImageMagick
wget ftp://ftp.imagemagick.org/pub/ImageMagick/ImageMagick.tar.gz
tar -zxvf Imagemagick.tar.gz
cd Imagemagick-6.7.6-8
./configure; make; make install

Then i got the problem magickwand.h not found or w.e. So i did
PKG_CONFIG_PATH=/usr/local/lib/pkgconfig/ gem install rmagick
{% endcodeblock %}
things work fine now.
* * *
now i met another problem when i run `rake`, the system teld me `sqlite3_native.so: undefined symbol: sqlite3_initialize` 
* * *
* i got a problem on how to open a port on Red Hat 5.4, finally i found the [solution](http://www.cyberciti.biz/faq/howto-rhel-linux-open-port-using-iptables/)
{% codeblock lang:bash %}
vi /etc/sysconfig/iptables
...do sth. here
/etc/init.d/iptables restart
{% endcodeblock %}
* * *
* another problem, the version of vim installed on Redhat 5.4 is 7.0, but i want version 7.3, but the repo in system is only 7.0, so i have to compile vim from source.
{% codeblock lang:bash %}
wget ftp://ftp.vim.org/pub/vim/unix/vim-7.3.tar.bz2
tar jxf vim-7.3.tar.bz2 
cd vim73
./configure --enable-cscope --enable-multibyte --enable-xim --enable-fontset --with-features=huge
make;make install
ln -sf `which vim` /bin/vi
{% endcodeblock %}
Fine, the vim 7.3 is installed, and you will find the syntax highlight and other vim-features now work.

