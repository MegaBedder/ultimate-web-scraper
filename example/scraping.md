# Scraping Webpages - The Easy Way

Webpages are hard to retrieve and harder to parse. And doing this consistently across a wide variety of web hosts and scenarios makes it very difficult to do this alone. The Ultimate Web Scraper Toolkit makes both retrieving and parsing webpages a whole lot easier.


Example direct ID usage:

```php
<?php
	require_once "support/web_browser.php";
	require_once "support/tag_filter.php";

	// Retrieve the standard HTML parsing array for later use.
	$htmloptions = TagFilter::GetHTMLOptions();

	// Retrieve a URL (emulating Firefox by default).
	$url = "http://www.somesite.com/something/";
	$web = new WebBrowser();
	$result = $web->Process($url);

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

	// Get the final URL after redirects.
	$baseurl = $result["url"];

	// Use TagFilter to parse the content.
	$html = TagFilter::Explode($result["body"], $htmloptions);

	// Find all anchor tags.
	echo "All the URLs:\n";
	$result2 = $html->Find("a[href]");
	if (!$result2["success"])
	{
		echo "Error parsing/finding URLs.  " . $result2["error"] . "\n";
		exit();
	}

	foreach ($result2["ids"] as $id)
	{
		// Faster direct access.
		echo "\t" . $html->nodes[$id]["attrs"]["href"] . "\n";
		echo "\t" . HTTP::ConvertRelativeToAbsoluteURL($baseurl, $html->nodes[$id]["attrs"]["href"]) . "\n";
	}

	// Find all table rows that have 'th' tags.
	// The 'tr' tag IDs are returned.
	$result2 = $html->Filter($hmtl->Find("tr"), "th");
	if (!$result2["success"])
	{
		echo "Error parsing/finding table rows.  " . $result2["error"] . "\n";
		exit();
	}

	foreach ($result2["ids"] as $id)
	{
		echo "\t" . $html->GetOuterHTML($id) . "\n\n";
	}
?>
```


Example object-oriented usage:

```php
<?php
	require_once "support/web_browser.php";
	require_once "support/tag_filter.php";

	// Retrieve the standard HTML parsing array for later use.
	$htmloptions = TagFilter::GetHTMLOptions();

	// Retrieve a URL (emulating Firefox by default).
	$url = "http://www.somesite.com/something/";
	$web = new WebBrowser();
	$result = $web->Process($url);

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

	// Get the final URL after redirects.
	$baseurl = $result["url"];

	// Use TagFilter to parse the content.
	$html = TagFilter::Explode($result["body"], $htmloptions);

	// Retrieve a pointer object to the root node.
	$root = $html->Get();

	// Find all anchor tags.
	echo "All the URLs:\n";
	$rows = $root->Find("a[href]");
	foreach ($rows as $row)
	{
		echo "\t" . $row->href . "\n";
		echo "\t" . HTTP::ConvertRelativeToAbsoluteURL($baseurl, $row->href) . "\n";
	}

	// Find all table rows that have 'th' tags.
	$rows = $root->Find("tr")->Filter("th");
	foreach ($rows as $row)
	{
		echo "\t" . $row->GetOuterHTML() . "\n\n";
	}
?>
```


These brief examples retrieve a URL while emulating some flavor of Firefox and display the value of the 'href' attribute of all anchor tags that have a 'href' attribute as well as finding all table rows with 'th' tags. In addition, because the WebBrowser class was used, the code will internally and automatically handle HTTP cookies and redirects.

You'll get lots of mileage out of the HTTP::ExtractURL(), HTTP::CondenseURL(), HTTP::ConvertRelativeToAbsoluteURL(), and other useful functions when extracting content from a HTML page and processing server responses.

See the following for in-depth documentation and extensive examples on performing document retrieval and extracting content with TagFilter: [WebBrowser classes documentation](/docs/web_browser.md), [TagFilter classes documentation](/docs/tag_filter.md), and [HTTP class documentation](/docs/http.md).


# Scraping Webpages - The Hard Way
The previous example used the web browser emulation layer (WebBrowser) to retrieve the content. Sometimes getting into the nitty-gritty details of constructing a web request is the desired option (but only in extremely rare situations).

Example:
```php
<?php
	require_once "support/http.php";
	require_once "support/tag_filter.php";

	// Retrieve the standard HTML parsing array for later use.
	$htmloptions = TagFilter::GetHTMLOptions();

	$url = "http://www.somesite.com/something/";
	$options = array(
		"headers" => array(
			"User-Agent" => HTTP::GetWebUserAgent("Firefox"),
			"Accept" => "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
			"Accept-Language" => "en-us,en;q=0.5",
			"Accept-Charset" => "ISO-8859-1,utf-8;q=0.7,*;q=0.7",
			"Cache-Control" => "max-age=0"
		)
	);
	$result = HTTP::RetrieveWebpage($url, $options);
	if (!$result["success"])  echo "Error retrieving URL.  " . $result["error"] . "\n";
	else if ($result["response"]["code"] != 200)  echo "Error retrieving URL.  Server returned:  " . $result["response"]["code"] . " " . $result["response"]["meaning"] . "\n";
	else
	{
		// Use TagFilter to parse the content.
		$html = TagFilter::Explode($result["body"], $htmloptions);

		// Find all anchor tags.
		echo "All the URLs:\n";
		$result2 = $html->Find("a[href]");
		if (!$result2["success"])  echo "Error parsing/finding URLs.  " . $result2["error"] . "\n";
		else
		{
			foreach ($result2["ids"] as $id)
			{
				// Fast direct access.
				echo "\t" . $html->nodes[$id]["attrs"]["href"] . "\n";
			}
		}

		// Find all table rows that have 'th' tags.
		// The 'tr' tag IDs are returned.
		$result2 = $html->Filter($hmtl->Find("tr"), "th");
		if (!$result2["success"])  echo "Error parsing/finding table rows.  " . $result2["error"] . "\n";
		else
		{
			foreach ($result2["ids"] as $id)
			{
				echo "\t" . $html->GetOuterHTML($id) . "\n\n";
			}
		}
	}
?>
```

This example performs the same operation as the previous section, but doesn't get all the benefits of the web browser emulation layer such as automatically handling redirects and cookies. You should, in general, prefer using the WebBrowser class.

See the [HTTP class documentation](/docs/http.md) for more in-depth details and examples.
