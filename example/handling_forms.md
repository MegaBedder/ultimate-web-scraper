# Handling HTML Forms

Traditionally, one of the hardest things to handle with web scraping is the classic HTML form. If you are like me, then you've generally just faked it and manually handled form submissions by just bypassing the form itself (i.e. manually copied variable names). The problem is that if/when the server side changes how it does things, the old form submission code will tend to break in spectacular ways. This toolkit includes several functions designed to make real form handling a walk in the park.


Example HTML form extraction:

```php
<?php
	require_once "support/web_browser.php";
	require_once "support/tag_filter.php";

	// Retrieve the standard HTML parsing array for later use.
	$htmloptions = TagFilter::GetHTMLOptions();

	$url = "https://www.somewebsite.com/login/";

	// Turn on the automatic forms extraction option.  Note that Javascript is not executed.
	$web = new WebBrowser(array("extractforms" => true));
	$result = $web->Process($url);

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

	if (count($result["forms"]) != 1)
	{
		echo "Was expecting one form.  Received:  " . count($result["forms"]) . "\n";
		exit();
	}

	// Forms are extracted in the order they appear in the HTML.
	$form = $result["forms"][0];

	// Set some or all of the variables in the form.
	$form->SetFormValue("username", "cooldude123");
	$form->SetFormValue("password", "password123");

	// Submit the form.
	$result2 = $form->GenerateFormRequest();
	$result = $web->Process($result2["url"], $result2["options"]);

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

	// Use TagFilter to parse the content.
	$html = TagFilter::Explode($result["body"], $htmloptions);

	// Do something with the response here...
?>
```

Note that, like the rest of the WebBrowser class, the form handler doesn't process Javascript. Very few sites actually need Javascript. For those rare, broken websites that need Javascript for the form on the page to function, I'm usually able to get away with a quick [regular expression](http://php.net/manual/en/function.preg-match.php) or two to pull the necessary information from the body content.

