# Retrieving Large Files/Content
Sometimes the content to retrieve is just too large to handle completely in RAM. The Ultimate Web Scraper Toolkit sports a very impressive array of callback options allowing for retrieved information to be processed immediately instead of waiting for the request to complete. The most common use-case for using the callback options is to handle large file/content downloads. When retrieving anything over 10MB, it's a good idea to start utilizing the callback interfaces.


Example large file/content retrieval:

```php
<?php
	require_once "support/web_browser.php";

	function DownloadFileCallback($response, $data, $opts)
	{
		if ($response["code"] == 200)
		{
			$size = ftell($opts);
			fwrite($opts, $data);

			if ($size % 1000000 > ($size + strlen($data)) % 1000000)  echo ".";
		}

		return true;
	}

	// Download a large file.
	$url = "http://downloads.somesite.com/large_file.zip";
	$fp = fopen("the_file.zip", "wb");
	$web = new WebBrowser();
	$options = array(
		"read_body_callback" => "DownloadFileCallback",
		"read_body_callback_opts" => $fp
	);
	echo "Downloading '" . $url . "'...";
	$result = $web->Process($url, $options);
	echo "\n";
	fclose($fp);

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

The example above passes a file handle through the callback options parameter. The callback is called regularly when data is received and the callback writes the retrieved data to the open file. It also determines if a 1MB boundary has been passed and, if so, it echos a dot/period out to the console.
