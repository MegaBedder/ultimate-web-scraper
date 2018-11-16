Sending Non-Standard Requests
-----------------------------

The vast majority of requests to servers are GET, POST application/x-www-form-urlencoded, and POST multipart/form-data.  However, there may be times that other request types need to be sent to a server.  For example, a lot of APIs being written these days want JSON content instead of a standard POST request to be able to handle richer incoming data.

Example:

```php
<?php
	require_once "support/web_browser.php";

	// Send a POST request with a custom body.
	$url = "http://api.somesite.com/profile";
	$web = new WebBrowser();
	$options = array(
		"method" => "POST",
		"headers" => array(
			"Content-Type" => "application/json"
		),
		"body" => json_encode(array(
			"id" => 12345,
			"firstname" => "John",
			"lastname" => "Smith"
		), JSON_UNESCAPED_SLASHES)
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

Working with such APIs is best done by building a SDK.  Here are several SDKs and their relevant API documentation that might be useful:

* [DigitalOcean SDK](https://github.com/cubiclesoft/digitalocean) and [API documentation](https://developers.digitalocean.com/documentation/)
* [OpenDrive SDK](https://github.com/cubiclesoft/cloud-backup/blob/master/support/sdk_opendrive.php) and [API documentation](https://www.opendrive.com/api)

The above SDKs utilize this toolkit.
