# Refined SSL Usage
By default, the Ultimate Web Scraper Toolkit verifies SSL certificate chains using the included 'support/cacert.pem' file when connecting to HTTPS URLs using the "intermediate" Mozilla cipher suite. Previously, the HTTP class did not do validation of a secure communication path due to being primarily a tool for scraping content where "working and functional" is generally more important than the security of the data being sent and received. However, due to several unfortunate changes and bugs, the policy was changed. Fortunately, you can now more reliably control the SSL options, including simpler cipher suite selection.

Where data security is of concern, keep in mind that SSL is hard to get right and best-practices change over time as evidenced by the previous paragraph.

Example custom SSL options usage:

```php
<?php
	require_once "support/http.php";
	require_once "support/web_browser.php";

	// Generate default safe SSL/TLS options using the "modern" ciphers.
	// See:  https://mozilla.github.io/server-side-tls/ssl-config-generator/
	$sslopts = HTTP::GetSafeSSLOpts(true, "modern");

	// Adjust the options as necessary.
	// For a complete list of options, see:  http://php.net/manual/en/context.ssl.php
	$sslopts["capture_peer_cert"] = true;

	// Demonstrates capturing the SSL certificate.
	// Returning false terminates the connection without sending any data.
	function CertCheckCallback($type, $cert, $opts)
	{
		var_dump($type);
		var_dump($cert);

		return true;
	}

	// Send a POST request to a URL.
	$url = "https://api.somesite.com/profile";
	$web = new WebBrowser();
	$options = array(
		"sslopts" => $sslopts,
		"peer_cert_callback" => "CertCheckCallback",
		"peer_cert_callback_opts" => false,
		"postvars" => array(
			"id" => 12345,
			"firstname" => "John",
			"lastname" => "Smith"
		)
	);
	$result = $web->Process($url, $options);

	// Check for connectivity and response errors.
	if (!$result["success"])
	{
		echo "Error retrieving URL.  " . $result["error"] . "\n";
		exit();
	}

	if ($result["response"]["code"] != 200)
	{
		echo "Error retrieving URL.  Server returned:  " . $result["response"]["code"] . " " . $result["response"]["meaning"] . "\n";
		exit();
	}

	// Do something with the response.
?>
```

This example uses the [Modern ciphers from Mozilla](https://mozilla.github.io/server-side-tls/ssl-config-generator/) which has certain security properties and the defaults also disable SSL/TLS compression and set a number of other default options to improve the likelihood of a successful connection to a SSL/TLS enabled server. The full list of available SSL options can be found in the [PHP SSL context options documentation](http://php.net/manual/en/context.ssl.php).
