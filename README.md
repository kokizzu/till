# DataHen Till

<img align="left" width="70" height="70" style="padding:0 12px 0 0;" src="img/icons8-spade.svg"> **DataHen Till** is a standalone tool that runs alongside your web scraper, and instantly makes your existing web scraper unblockable, scalable, and maintainable, without requiring any code changes on your scraper code. 

Based on [DataHen](https://www.datahen.com)'s experience in almost a decade of scraping billions of pages from thousands of some of the top largest websites in the world, we realized that there's got to be a better way so that the typical developers can easily build web scrapers in their preferred programming language, and be able to scale their scrapers easily.

Till is designed primarily to increase developer happiness, and to follow best practices that DataHen has accumulated over the years of scraping at a massive scale.

## Problems with Web Scraping


Web scraping is easy to get started but proved to be very difficult to master at scale. Scraping 10,000 records can easily be done with simple web scraper scripts in any language, but imagine trying to scrape millions or even billions of pages. You would need to architect and build features that allow you to unblock, scale and maintain your scrapers. 


The following problems related to scaling are what **DataHen Till** solves:


### Scaling your scraper
Scraping to millions or even billions of records requires much more pre-planning. It's not simply running your existing web scraper script in a bigger CPU/Ram machine. 
More thoughts are needed, such as: 

- How to do logging at 
- Where to store the data
- The bandwidth used 
- Rotating through IP proxies
- File system load
- etc.

Although Till does not try to solve all of your scaling needs, Till provides a plug-and-play method of making your web scrapers with best practices when it comes to scaling web scrapers. These best practices include logging HTTP requests, caching HTTP requests and reusing them as needed, Randomizing user agents, and proxies, and also making your scraper code troubleshooting and maintenance a pleasant experience. 

### Blocked scraper
As you try to scale up the number of requests, quite often, the target websites will often detect that you are web scraping and try to block your requests using Captcha, or throttling, or denying your request completely.
Especially if those websites are pretty popular, they would've taken enough steps to ensure that they don't get scraped.

Till helps you get around being detected as a web scraper by identifying your scraper as a real web browser. It does this by generating random `user-agent` headers and randomizing proxy IPs (that you supply) on every HTTP request. 

### Scraper Maintenance
Maintaining highly scalable scrapers is not that easy. Because of the sheer number of requests that are being made, and the number of interactions between your scrapers and the target websites, for it to run smoothly, you need to think through how to maintain your scrapers regularly. You need to know how to raise and triage errors as they occur on your scrapers, not all errors on web scraping should be treated equally. some are ignorable, and some are urgent. So, you will need to know what will be the details of your development-deployment-maintenance process will be.

Till solves this by logging all your HTTP requests and categorizing them whether it was successful (2XX statuses) or failures(non 2XX statuses). Till also makes it even easier for scraper maintenance by marking each request with a unique Global ID (GID) that is derived from the request's URL, method, body, etc. You can then use this GID to troubleshoot your scrapers on where it went wrong.

### Postmortem analysis & reproducability
The biggest difficulty facing any web scraper developer is when there are scraping failures. Your scraper fails when fetching or parsing certain URLs, but when you look at the target website and URLs, everything looks fine. How do you troubleshoot what already happened in the scenario?. How do you reproduce that failed scrape so that you can fix the issue?

Till stores all HTTP requests and the responses (including the response body/content) into a local cache. If at anytime your scraper encounters an error, you can then use the request's GID (Till assigns a Global ID, also called GID, on every request) to find the request and the actual response and content from the cache. In this way, you can analyze what went wrong with that particular request.

### Starting over from scratch when it fails mid-way
Websites change all the time and without notice. Imagine running your web scraper for a week and then suddenly, somewhere along the way, it fails. It is frustrating that once you've fixed the scraper, there is a high chance that you'd need to start over from scratch again. And, on top of this, there are additional consequences, such as time delay, and further charges related to proxy usage, etc. 

Because Till assigns all HTTP requests their own Global ID (GID), and also stores all responses in the Cache, your scraper can then "replay" the scraping process without actually needing to do another set of requests to the target website. Till will simply serve the cached response to your scraper, and whenever a cached response is not found, it will do a real request to the target website.
## Features
### Automatic Retries
You don't have to write the retry logic in your scraper code, Till will retry your request up to 60 seconds (or however you wish). All you need to do is make sure that your scraper's timeout accommodates this.
### Managing Cookies
No need to build your cookie management logic in your scraper codes. Till stores the cookies for you so that you can easily set and get the cookies on any request.

### User-Agent randomizer 
Till automatically generates random user-agent on every request. Choose to identify your scraper as a desktop browser, or a mobile browser, or you can even override it with your custom user-agent.

### Proxy IP address rotation
Supply a list of proxy IPs, and Till will randomly use them on every request. Saves you time in needing to set up a separate proxy rotation service.

### Advanced Logging
Till will log your requests based on if it's a successful request (2XX status code) or failed request (non 2XX status code). This will allow you to easily troubleshoot your scraper later. You can also export the log in the [HAR](https://en.wikipedia.org/wiki/HAR_(file_format)) format, and you can open this in your Chrome Browser's (or other browsers) inspector tool.

### HTTP Response caching
Till will cache all your HTTP responses (and their contents) locally, so that when you need run your scraper again, Till will reuse the same cached response and contents without needing to do an actual request to the target server. You can even specify the freshness criteria of the cached contents to use. If the cache is outside of your freshness criteria, Till will send a real request to the target server and store that in the cache. 

### Global Identifier (GID) for every unique request
Till uses [DataHen Scrape Platform](https://www.datahen.com/platform)'s convention of marking every unique request with a signature (we call this the Global ID or GID for short). Think of it like a Checksum of the actual request. 

Anytime your scraper sends a request through Till, it will return a response with the header `X-DH-GID` that contains the GID. This GID allows you to easily troubleshoot requests when you need to look up specific requests in the log, or contents in the cache.


## How DataHen Till works

Till works as a Man In The Middle (MITM) proxy that listens to incoming HTTP(S) requests and forwards those requests to the target server as needed. While it does so, it enhances each request to increase unblockability. It also logs and caches the responses to make your scraper maintainable and scalable.

Connect your scraper to Till via the `proxy` protocol that is typically common in any programming language.

Your scraper will then continue to run as-is and it will get instantly become more unblockable, scalable, and maintainable.


## Installation

The recommended way to install DataHen Till is by downloading the standalone binaries on [this link](https://github.com/DataHenHQ/till/releases).


## Certificate Authority (CA) Certificates
Till decripts and encrypts HTTPS traffic on the fly between your scraper and the target websites.  In order to do so, your scraper (or browser) must be able to trust the built-in Certificate Authority (CA). This means, the CA certificates that Till generates for you needs to be installed on the computer where the scraper is running.

**Note:** If you do not wish to install the CA certificates, you can still have your scraper connect to the Till server by disabling/ignoring security checks in your scraper. Please refer to the programming language/framework/tool that your scraper uses.

### Installing the generated CA certificates onto your computer
The first time Till runs as a server, Till generates the CA certificates in the following directory: 

Linux or MacOS:
```
~/.config/datahen/till/cert/
```

Windows:
```
%APPDATA%/datahen/till/cert
```
Then, please follow the following instructions to install the CA certificates:
#### MacOS

[Add certificates to a keychain using Keychain Access on Mac](https://support.apple.com/en-ca/guide/keychain-access/kyca2431/mac)

#### Ubuntu/Debian
[How do I install a root certificate](https://askubuntu.com/questions/73287/how-do-i-install-a-root-certificate/94861#94861)

#### Mozilla Firefox
[how to import the Mozilla Root Certificate into your Firefox web browser](https://wiki.mozilla.org/MozillaRootCertificate#Mozilla_Firefox)

#### Chrome
[Getting Chrome to accept self-signed localhost certificate](https://stackoverflow.com/questions/7580508/getting-chrome-to-accept-self-signed-localhost-certificate/15076602#15076602)

#### Windows
Use `certutil` with the following command:

```
certutil -addstore root <path to your CA cert file>
```

Read more about [certutil](https://web.archive.org/web/20160612045445/http://windows.microsoft.com/en-ca/windows/import-export-certificates-private-keys#1TC=windows-7)

## Usage


After installing, start the Till server with the following command
```bash
$ till serve # this will run Till in port 2933
```

After you have started Till, your scraper code can then connect to Till via the `proxy` protocol.
### Curl

To connect using Curl, you can do the following:

```bash
$ curl -k --proxy http://localhost:2933 https://fetchtest.datahen.com/echo/request
```