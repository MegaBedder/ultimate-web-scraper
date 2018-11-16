Ultimate Web Scraper Toolkit
============================

A PHP library of tools designed to handle all of your web scraping needs under a MIT or LGPL license.  This toolkit easily makes RFC-compliant web requests that are indistinguishable from a real web browser, has a web browser-like state engine for handling cookies and redirects, and a full cURL emulation layer for web hosts without the PHP cURL extension installed.  The powerful tag filtering library TagFilter is included to easily extract the desired content from each retrieved document or used to process HTML documents that are offline.

This tookit also comes with classes for creating custom web servers and WebSocket servers.  That custom API you want the average person to install on their home computer or deploy to devices in the enterprise just became easier to deploy.

[![Donate](https://cubiclesoft.com/res/donate-shield.png)](https://cubiclesoft.com/donate/)

Features
--------

* Carefully follows the IETF RFC Standards surrounding the HTTP protocol.
* Supports file transfers, SSL/TLS, and HTTP/HTTPS/CONNECT proxies.
* Easy to emulate various web browser headers.
* A web browser-like state engine that emulates redirection (e.g. 301) and automatic cookie handling for managing multiple requests.
* HTML form extraction and manipulation support.  No need to fake forms!
* Extensive callback support.
* Asynchronous/Non-blocking socket support.  For when you need to scrape lots of content simultaneously.
* WebSocket support.
* A full cURL emulation layer for drop-in use on web hosts that are missing cURL.
* An impressive CSS3 selector tokenizer (TagFilter::ParseSelector()) that carefully follows the W3C Specification and passes the official W3C CSS3 static test suite.
* Includes a fast and powerful tag filtering library (TagFilter) for correctly parsing really difficult HTML content (e.g. Microsoft Word HTML) and can easily extract desired content from HTML and XHTML using CSS3 compatible selectors.
* TagFilter::HTMLPurify() produces XSS defense results on par with HTML Purifier.
* Includes the legacy Simple HTML DOM library to parse and extract desired content from HTML.  NOTE:  Simple HTML DOM is only included for legacy reasons.  TagFilter is much faster and more accurate as well as more powerful and flexible.
* An unncessarily [feature-laden web server class](https://github.com/cubiclesoft/ultimate-web-scraper/blob/master/docs/web_server.md) with optional SSL/TLS support.  Run a web server written in pure PHP.  Why?  Because you can, that's why.
* A decent [WebSocket server class](https://github.com/cubiclesoft/ultimate-web-scraper/blob/master/docs/websocket_server.md) is included too.
* Has a liberal open source license.  MIT or LGPL, your choice.
* Designed for relatively painless integration into your project.
* Sits on GitHub for all of that pull request and issue tracker goodness to easily submit changes and ideas respectively.

Getting Started
---------------

[![Web Scraping - Techniques and tools of the trade](https://user-images.githubusercontent.com/1432111/42725116-523907e6-8733-11e8-8322-71631f5e198a.png "Watch video")](https://www.youtube.com/watch?v=54tB8t1r0og)

Examples:

- [Scraping Webpages](/example/scraping.md)

- [HTML form extraction](/example/handling_forms.md)

- [POST request](/example/post_request.md)

- [large file/content retrieval](/example/large_files.md)

- [custom SSL options](/example/custom_ssl.md)

- [debug mode (including SSL/TLS)](/example/debugging.md)

- [Uploading Files](/example/uploading_files.md)

- [Sending Non-Standard Requests](/example/custom_requests.md)

- [Handling Pagination](/example/handling_pagination.md)


Limitations
-----------

The only real limitation with Ultimate Web Scraper Toolkit is its inability to process Javascript.  A simple regex here and there to extract data hardcoded via Javascript usually works well enough.

For the 0.5% of websites where there is useful content to scrape but the entire page content is generated using Javascript or is protected by Javascript in unusual ways, a real web browser is required.  Fortunately, there is [PhantomJS](http://phantomjs.org/) (headless Webkit), which can be scripted (i.e. automated) to handle the aforementioned Javascript-heavy sites.  However, PhantomJS is rather resource intensive and __slow__.  After all, PhantomJS emulates a real web browser which includes the full startup sequence and then it proceeds to download the entire page's content.  That, in turn, can take hundreds of requests to complete and can easily include downloading things such as ads.

It is very rare though to run into a website like that.  Ultimate Web Scraper Toolkit can handle most anything else.

More Information
----------------

Full documentation and more examples can be found in the '[docs](/docs)' directory of this repository.
