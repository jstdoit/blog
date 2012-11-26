---
layout: post
title: "xmpp4r PUBSUB hack"
date: 2012-11-26 06:03
comments: true
categories: [Server, XMPP, Ruby]
---
In my current [Project](http://about.yuncaipu.com), I need to use XMPP sending notification to android client, so I implemented a xmpp server using [Node.js](http://nodejs.org),
and using [xmpp4r-0.5](http://home.gna.org/xmpp4r/) to login as publisher to send notification to server. They all work well before i migrated all application to new server.
Whenever I send msg, the xmpp4r always throws exceptions. Below is the trace:
<!-- more -->
{% codeblock lang:bash %}
W, [2012-11-26T11:59:55.734074 #29746]  WARN -- : EXCEPTION:
    REXML::UndefinedNamespaceException
    Undefined prefix stream found
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/rexml/parsers/baseparser.rb:406:in `block in pull_event'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/set.rb:222:in `block in each'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/set.rb:222:in `each_key'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/set.rb:222:in `each'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/rexml/parsers/baseparser.rb:404:in `pull_event'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/rexml/parsers/baseparser.rb:183:in `pull'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/rexml/parsers/sax2parser.rb:92:in `parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/streamparser.rb:79:in `parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:75:in `block in start'
		W, [2012-11-26T11:59:55.734243 #29746]  WARN -- : EXCEPTION:
    TypeError
    can't convert REXML::UndefinedNamespaceException into String
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:137:in `+'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:137:in `parse_failure'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/streamparser.rb:81:in `rescue in parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/streamparser.rb:38:in `parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:75:in `block in start'
		W, [2012-11-26T11:59:55.734326 #29746]  WARN -- : Exception caught in Parser thread! (TypeError)
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:137:in `+'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:137:in `parse_failure'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/streamparser.rb:81:in `rescue in parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/streamparser.rb:38:in `parse'
    /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:75:in `block in start'
	/usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/thread.rb:71:in `sleep': deadlock detected (fatal)
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/1.9.1/thread.rb:71:in `wait'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/semaphore.rb:24:in `block in wait'
	from <internal:prelude>:10:in `synchronize'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/semaphore.rb:23:in `wait'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:331:in `wait'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/stream.rb:398:in `send'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/client.rb:88:in `start'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/client.rb:175:in `auth_sasl'
	from /usr/local/rvm/rubies/ruby-1.9.3-p194/lib/ruby/site_ruby/1.9.1/xmpp4r/client.rb:112:in `auth'
	from ./pub-just:18:in `<main>'
{% endcodeblock %}
Of course I don't think that Ruby's sax2parser makes my app go wrong. So I checked the xmpp4r's code. From the exception , i can find that tha problem occurs after the `auth` stanza,
then I checked the src-code from `XMPP4R_GEM_PATH/client.rb`, and find the code after auth.
{% codeblock "XMPP4R_GEM_PATH/client.rb" lang:ruby %}
def auth_sasl(sasl, password)
	sasl.auth(password)
	# Restart stream after SASL auth
	stop
	start
	# And wait for features - again
	@features_sem.wait

	# Resource binding (RFC3920 - 7)
	if @stream_features.has_key? 'bind'
		@jid = bind(@jid.resource)
	end 

	# Session starting
	if @stream_features.has_key? 'session'
		iq = Iq.new(:set)
		session = iq.add REXML::Element.new('session')
		session.add_namespace @stream_features['session']

		send_with_id(iq)
	end 
end 
{% endcodeblock %}
there is one `stop` and `start` after auth. So I traced the `stop` and `start`, and found the `stop` implemented in file `XMPP4R_GEM_PATH/stream.rb` as follows:
{% codeblock "XMPP4R_GEM_PATH/stream.rb" lang:ruby %} 
def stop
	@parser_thread.kill
	@parser = nil 
end
{% endcodeblock %}
and the `@parser_thread` is obviously a Thread, so the problem must be caused by the thread's mech, it must be when the thread's not being killed entirely we call the method `start`, so I hacked the code via add a line `while @parser_thread.alive? do end` and modify the code above in file `XMPP4R_GEM_PATH/client.rb` as follows:
{% codeblock "XMPP4R_GEM_PATH/client.rb" lang:ruby %}
def auth_sasl(sasl, password)
	sasl.auth(password)
	# Restart stream after SASL auth
	stop
	while @parser_thread.alive? do 
	end
	start
	# And wait for features - again
	@features_sem.wait

	# Resource binding (RFC3920 - 7)
	if @stream_features.has_key? 'bind'
		@jid = bind(@jid.resource)
	end 

	# Session starting
	if @stream_features.has_key? 'session'
		iq = Iq.new(:set)
		session = iq.add REXML::Element.new('session')
		session.add_namespace @stream_features['session']

		send_with_id(iq)
	end 
end 
{% endcodeblock %}
Until now, my code is working normally, hope this help you someday.
