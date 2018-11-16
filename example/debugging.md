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
