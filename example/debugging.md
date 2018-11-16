Debugging
---------

The this section covers issues that occur AFTER a connection to a server has been established. 

Got an API or website that's driving you crazy? A real web browser seems to work fine but your script isn't working? It might be time to dig in really deep and enable debug mode.


Example debug mode usage:

```php
<?php
	require_once "support/web_browser.php";

	// Send a POST request to a URL with debugging enabled.
	// Enabling debug mode for a request uses more RAM since it collects all data sent and received over the wire.
	$url = "https://api.somesite.com/profile";
	$web = new WebBrowser();
	$options = array(
		"debug" => true,
		"postvars" => array(
			"id" => 12345,
			"firstname" => "John",
			"lastname" => "Smith"
		)
	);
	$result = $web->Process($url, $options);

	// Check for connectivity errors.
	if (!$result["success"])
	{
		echo "Error retrieving URL.  " . $result["error"] . "\n";
		exit();
	}

	echo "------- RAW SEND START -------\n";
	echo $result["rawsend"];
	echo "------- RAW SEND END -------\n\n";

	echo "------- RAW RECEIVE START -------\n";
	echo $result["rawrecv"];
	echo "------- RAW RECEIVE END -------\n\n";
?>
```

The "rawsend" and "rawrecv" show the exact byte-for-byte data sent and received, including any "chunked" information (i.e. Transfer-Encoding: chunked). Debug mode will also show data sent and received over SSL as plain-text, which makes it a better tool than Wireshark or an equivalent raw TCP dumping tool that can't see beyond a SSL handshake. Be sure to disable debug mode once the problem is resolved or it will chew up extra RAM.


Debugging SSL/TLS
-----------------

Connecting to a SSL/TLS enabled server is fraught with difficulties.  SSL/TLS connections are much more fragile through no fault of the toolkit but rather SSL/TLS doing its thing.  Here are the known reasons a SSL/TLS connection will fail to establish:

* Network failures.  The server gives up because SSL/TLS is expensive on both local and remote system resources (mostly CPU), there is a temporary network condition (just retry the request), or the request is being actively blocked by a firewall (e.g. port blocking for a range of abusive IPs).
* The server (or client) SSL/TLS certificate is incomplete or does not validate against a known root CA certificate list.
* The server (or client) SSL/TLS certificate has expired.  Not much can be done here except completely disable SSL validation.
* A bug in Ultimate Web Scraper Toolkit exposed due to underlying TLS bugs in PHP.  This is really rare though.

PHP does not expose much of the underlying SSL/TLS layer to applications when establishing connections, which makes it incredibly difficult to diagnose certain issues with SSL/TLS.  To diagnose network related problems, use the 'openssl s_client' command line tool from the same host the problematic script is running on.  

Once the possibility of a network failure has been eliminated, only two common SSL/TLS certificate issues generally remain. See the section on [Refined SSL Usage](/example/custom_ssl.md) above for setting the "cafile", "auto_cn_match", and "auto_sni" SSL options.

If all else fails and secure, encrypted communication with the server are not required, disable the "verify_peer" and "verify_peer_name" SSL options and enable the "allow_self_signed" SSL option.  Note that making these changes results in a connection that is no more secure that plaintext HTTP.  Don't send passwords or other information that should be kept secure.  This solution should only ever be used as a last resort.  Always try to get the toolkit working with verification first.
