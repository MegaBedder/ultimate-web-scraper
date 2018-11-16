# POST Requests
Sometimes you might need to send a POST request that isn't from a form. For example, you might be writing a SDK for a RESTful API or emulating an AJAX interface. To send a POST request, simply build an options array with a "postvars" array with key-value pairs containing the information that the server requires.

Example POST request:

```php
<?php
	require_once "support/web_browser.php";

	$url = "https://api.somesite.com/profile";

	// Send a POST request to a URL.
	$web = new WebBrowser();
	$options = array(
		"postvars" => array(
			"id" => 12345,
			"firstname" => "John",
			"lastname" => "Smith"
		)
	);

	$result = $web->Process($url, $options);

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

All of the details of sending the correct headers and content to the server are automatically handled by the WebBrowser and HTTP classes.

File uploads are handled several different ways so that very large files can be processed. The "files" option is an array of arrays that represents one or more files to upload. Note that file uploads will switch a POST request's Content-Type from "application/x-www-form-urlencoded" to "multipart/form-data".
