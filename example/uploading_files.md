Uploading Files
---------------

File uploads are handled several different ways so that very large files can be processed.  The "files" option is an array of arrays that represents one or more files to upload.  File uploads will automatically switch a POST request's `Content-Type` from "application/x-www-form-urlencoded" to "multipart/form-data".

```php
<?php
	require_once "support/web_browser.php";

	// Upload two files.
	$url = "http://api.somesite.com/photos";
	$web = new WebBrowser();
	$options = array(
		"postvars" => array(
			"uid" => 12345
		),
		"files" => array(
			array(
				"name" => "file1",
				"filename" => "mycat.jpg",
				"type" => "image/jpeg",
				"data" => file_get_contents("/path/to/mycat.jpg")
			),
			array(
				"name" => "file2",
				"filename" => "mycat-hires.jpg",
				"type" => "image/jpeg",
				"datafile" => "/path/to/mycat-hires.jpg"
			)
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

Each file in the "files" array must have the following options:

* name - The server-side key to use.
* filename - The filename to send to the server.  Well-written server-side software will generally ignore this other than to look at the file extension (e.g. ".jpg", ".png", ".pdf").
* type - The MIME type to send to the server.  Run a Google search for "mime type for xyz" where "xyz" is the file extension of the file you are sending.

One of the following options must also be provided for each file:

* data - A string containing the data to send.  This should only be used for small files.
* datafile - A string containing the path and filename to the data to send OR a seekable file resource handle.  This is the preferred method for uploading large files.  Files exceeding 2GB may have issues under 32-bit PHP.

File uploads within extracted forms are handled similarly to the above.  When calling `$form->SetFormValue()`, pass in an array containing the file information with "filename", "type", and "data" or "datafile".  The "name" key-value will automatically be filled in when calling `$form->GenerateFormRequest()`.
