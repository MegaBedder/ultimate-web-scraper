
Handling Pagination
-------------------

There is a common pattern in the scraping world:  Pagination.  This is most often seen when submitting a form and the request is passed off to a basic search engine that usually returns anywhere from 10 to 50 results.

Unfortunately, you need all 8,946 results for the database you are constructing.  There are two ways to handle the scenario:  Fake it or follow the links/buttons.

"Faking it" eliminates the need to handle pagination in the first place.  What is meant by this?  Well, a lot of GET/POST requests in pagination scenarios pass along the "page size" to the server.  Let's say 50 results are being returned but the number '50' in a size attribute is also being sent to the server either on the first page or subsequent pages in the URL.  Well, what happens if the value '10000' is sent for the page size instead of '50'?  About 85% of the time, the server-side web facing software assumes it will only be passed the page size values provided in some client-side select box.  Therefore, the server-side just casts the submitted value to an integer and passes it along to the database AND does all of its pagination calculations from that submitted value.  The result is that all of the desired server-side data can be retrieved with just one request.  Frequently, if the page size is not in the first page of search results, page 2 of those search results will generally reveal what parameter is used for page size.  The ability to fake it on such a broad scale just goes to show that writing a functional search engine is a difficult task for a lot of developers.

But what if faking it doesn't work?  There's plenty of server-side software that can't handle processing/returning lots of data and will instead return an error - for example, with experimenting, maybe a server fails to return more than 3,000 rows at a time but that's still significantly more than 50 rows at a time.  Or the developer wrote their code to assume that their data might get scraped and forces the upper limit on the page size anyway.  Doing so just hurts them more than anything else since the scraping script will end up using more of their system resources to retrieve the same amount of data.  Regardless, if the data can't be retrieved all at once, pagination at whatever limit is imposed by the server is the only option.  If the requests are just URL-based, then pagination can be done by manipulating the URL.  If the requests are POST-based, then extracting forms from the page may be required.  It depends entirely on how the search engine was constructed.

Example:

```php
<?php
	require_once "support/web_browser.php";
	require_once "support/tag_filter.php";

	// Retrieve the standard HTML parsing array for later use.
	$htmloptions = TagFilter::GetHTMLOptions();

	$url = "http://www.somesite.com/something/?s=3000";
	$web = new WebBrowser(array("extractforms" => true));

	do
	{
		// Retrieve a URL.
		$retries = 3;
		do
		{
			$result = $web->Process($url);
			$retries--;
			if (!$result["success"])  sleep(1);
		} while (!$result["success"] && $retries > 0);

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

		$baseurl = $result["url"];

		// Use TagFilter to parse the content.
		$html = TagFilter::Explode($result["body"], $htmloptions);

		// Retrieve a pointer object to the root node.
		$root = $html->Get();

		$found = false;
		// Attempt to extract information.
		// Set $found to true if there is at least one row of data.

		if ($found)
		{
			$row = $root->Find("div.pagination a[href]")->Filter("/~contains:Next")->current();
			if ($row === false)  break;

			$url = HTTP::ConvertRelativeToAbsoluteURL($baseurl, $row->href);
		}
	} while ($found);
?>
```

One other useful tip is to attempt to use wildcard SQL characters or text patterns to extract more data than the website operator likely intended.  If a search box requires some field to be filled in for a search to be accepted, try a single '%' to see if the server is accepting wildcard LIKE queries.  If not, then maybe walking through the set of possible alphanumeric values will work (e.g. "a", "b", "c", "d") and then being careful to exclude duplicated data (e.g. "XYZ, Inc." would show up in six different search result sets).

Another useful tip is to be aware of URLs for detail pages.  For example, when viewing details about an item from a search and the item has "id=2018000001" in the URL for that page and then another item has "id=2017003449", then there may be a predictable pattern of year + sequence within that year as part of the ID for any given item.  Searching may not even be necessary as it may be possible to generate the URL dynamically (e.g. "id=2018000001", "id=2018000002", "id=2018000003") if the goal is to copy all records.
