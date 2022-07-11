# ca-police-websites
California police and law enforcement agencies that upload SB 1421 data on their websites


## nextrequest.com

1. Since nextrequest.com is used by a lot of agencies and since nextrequest subdomains are the respective pages, I sought to find the number of subdomains of nextrequest.
2. I found all the subdomains of nextrequest.com using a python script called [Knock](https://github.com/guelfoweb/knock).
  - Here’s how I installed Knock.
  - On Terminal, I installed Knock using the following code:
 ```git clone https://github.com/guelfoweb/knock.git
cd knock
pip3 install -r requirements.txt
python3 knockpy.py nextrequest.com 
```
  - A JSON file containing all the subdomains of nextrequest.com is created. 
  
### Convert JSON to CSV

1. I converted the large JSON file to CSV using the website: `https://www.convertcsv.com/json-to-csv.htm`
2. On the website, I used *Choose file* option to upload the JSON file and clicked on the *Convert JSON to CSV* 
3. The file was then converted to CSV, which I downloaded and then imported on [the main spreadsheet](https://docs.google.com/spreadsheets/d/1Et7lItppQ--FQt5r9kbKC82Y9pRjUzbXeD-0_v2nPgQ/edit#gid=783125507) as a new sheet titled *nextrequest.com*

### Data cleaning — getting rid of 302

1. On the spreadsheet, I had more than 4,000 URLs to possible nextrequest subdomains, but most of them are actually 302 redirects. 
2. I then wanted to get rid of all the 302 redirects and keep only 200s. But to do so, I needed to identify the status codes of all 4,000+ URLs.
3. I used the [tip](https://banhawy.medium.com/how-to-use-google-spreadsheets-to-check-for-broken-links-1bb0b35c8525) offered by a blogger.
4. On the spreadsheet’s *Extension* option, I clicked on *Apps Script*.
5. In the resultant page, I removed all the existing code and pasted the following:

```function getStatusCode(url) {
  var url_trimmed = url.trim();
  // Check if script cache has a cached status code for the given url
  var cache = CacheService.getScriptCache();
  var result = cache.get(url_trimmed);
  
  // If value is not in cache/or cache is expired fetch a new request to the url
  if (!result) {

    var options = {
      'muteHttpExceptions': true,
      'followRedirects': false
    };
    var response = UrlFetchApp.fetch(url_trimmed, options);
    var responseCode = response.getResponseCode();

    // Store the response code for the url in script cache for subsequent retrievals
    cache.put(url_trimmed, responseCode, 21600); // cache maximum storage duration is 6 hours
    result = responseCode;
  }

  return result;
}
```
  6. I created a new column next to the URLs titled *Status Code*. On the first cell, I typed the following function: `=getstauscode(b2)` — where (b2) refers to the cell that contains the URL. 
  7. All the URLs came back as 302. That is because, I realized, all the URLs had *http* protocol, instead of *https*.

### Converting all *http*s to *https*

1. I copied all the URLs, which didn’t have any *http*. I pasted all of them in a new column only as *values*. I created a new column and filled all the cells with *https://*.
2. I then combined two columns — the one that had text values of all URLs without hyperlinks and the one that had *https://* — using this formula: `=A2&""&B2`
3. All URLs became hyperlinks — except with *https*


### Re-run status coce

1. I then re-employed the `=getstauscode(b2)` formula.
2. It now correctly stated the status codes of all URLs. 
3. I found only 48 URLs with valid 202 code; only a handful of error/404 codes, which I checked manually and removed manually. The remaining URLs were 302 redirects.
4. I used filtered out 48 valid URLs and copied them, and pasted them into a new tab/sheet titled *valid_nextrequest*

### H1 of valid URLs

1. On the new sheet titled *valid_nextrequest*, I created a new column next to *status code*. 
2. On the new column, I used the formula `=IMPORTXML(C2,"//h1")` — where C2 refers to the cell that contains hyperlink of a website — to fetch all *h1* information of the websites. 
3. I found only a handful of errors; websites that no longer use the service have the errors. 
4. After getting rid of the errors, I found 42 valid nextrequest subdomains
5. Since the number of valid subdomains is low, I manually identified URLs of agencies in California.
