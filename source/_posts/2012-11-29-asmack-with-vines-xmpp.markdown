---
layout: post
title: "asmack with vines xmpp"
date: 2012-11-29 04:23
comments: true
categories: 
---
http://stackoverflow.com/questions/11712671/smack-no-response-from-server-not-sure-why-am-i-getting-this-error
I am keen to program with Ruby, so i used (Vines)[http://getvines.com] as my xmpp server, though it is not implemented entirely.
When i use (asmack)[https://github.com/Flowdalic/asmack] client library to connect to Vines xmpp server, i got this exception:
{% codeblock lang:bash %}
11-30 09:36:46.518: W/System.err(2174): java.security.KeyStoreException: java.security.NoSuchAlgorithmException: KeyStore jks implementation not found
11-30 09:36:46.518: W/System.err(2174):		at java.security.KeyStore.getInstance(KeyStore.java:119)
11-30 09:36:46.518: W/System.err(2174):		at org.jivesoftware.smack.ServerTrustManager.<init>(ServerTrustManager.java:71)
11-30 09:36:46.518: W/System.err(2174):		at org.jivesoftware.smack.XMPPConnection.proceedTLSReceived(XMPPConnection.java:824)
11-30 09:36:46.518: W/System.err(2174):		at org.jivesoftware.smack.PacketReader.parsePackets(PacketReader.java:262)
11-30 09:36:46.528: W/System.err(2174):		at org.jivesoftware.smack.PacketReader.access$000(PacketReader.java:43)
11-30 09:36:46.528: W/System.err(2174):		at org.jivesoftware.smack.PacketReader$1.run(PacketReader.java:69)
11-30 09:36:46.528: W/System.err(2174): Caused by: java.security.NoSuchAlgorithmException: KeyStore jks implementation not found
11-30 09:36:46.528: W/System.err(2174):		at org.apache.harmony.security.fortress.Engine.notFound(Engine.java:177)
11-30 09:36:46.528: W/System.err(2174):		at org.apache.harmony.security.fortress.Engine.getInstance(Engine.java:151)
11-30 09:36:46.528: W/System.err(2174):		at java.security.KeyStore.getInstance(KeyStore.java:116)
11-30 09:36:46.538: W/System.err(2174):		... 5 more
{% endcodeblock %}
it seems that the low-version android does not support jks algo, so by adding hack as follows before connection:
{% codeblock lang:java %}
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
		config.setTruststoreType("AndroidCAStore");
		config.setTruststorePassword(null);
		config.setTruststorePath(null);
} else {
		config.setTruststoreType("BKS");
		String path = System.getProperty("javax.net.ssl.trustStore");
		if (path == null)
				path = System.getProperty("java.home") + File.separator + "etc"
						+ File.separator + "security" + File.separator
						+ "cacerts.bks";
		config.setTruststorePath(path);
}
{% endcodeblock %}
* * *
