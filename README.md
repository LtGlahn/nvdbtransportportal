---
order: 1
---
# Getting started

The NVDB API is a REST api with extensive search- and filter capabilities, as well as options for fine tuning which 
data elements to include in the respons. 

[Vegkart](vegkart.md) is your best tool for digging into NVDB data, getting famiiar with the data structure and content. 
The CSV download capabilities of Vegkart is also sufficient for many use cases **- but vegkart is also an excellent training ground for API queries**: _Vegkart provides the link to the exact same API query that Vegkart uses to fetch data for you. This is an excellent starting point for learning the API functionality._ 

[This article](https://www.vegdata.no/ofte-stilte-sporsmal/hvordan-far-jeg-nvdb-data-inn-i-kartsystemet-mitt/) (in Norwegian) also 
gives an overview and recipies for how to get NVDB data into your mapping application / GIS.

Below, we give sufficient details of how to communicate with the API. We also highly recomend that you read these pages: 
  * [NVDB data catalogue](konsept2_datakatalog.md) How to find your way among 400 feature types
  * [NVDB features](konsept3_vegobjekter.md) Getting the actual data throught the `/vegobjekter` end point 
  * [NVDB reference metering](konsept4_roadreference.md) Labeling the road network with category, numbers, sections and meters. 
  * [NVDB network model](konsept5_network.md) Network data set for navigations - or nodes, link sequences and links, oh my

## Formats

The response formats from the API may be either XML or JSON (default), which you chooose using the 
standard http `accept` header. The format is versioned, and to ensure your application 
does not break upon format improvements we highly recomend you explsit control the version number, using this syntax: 
```
Accept: application/vnd.vegvesen.nvdb-v3-rev1+json
Accept: application/vnd.vegvesen.nvdb-v3-rev1+xml
```

The standard media types `application/json` and ` application/xml` will always return the newest format version.


## Tracking requests and sessions

At least one of these http headers must be set by the client: 
```
"X-Client" :  "your@email", or other identification
"User-Agent" : "Name of your fantastic system" 
```
We really, really appreciate if at least one of these, preferably both, are given meaningful names. It gives us valuable 
information about who uses our API, and for what purpose. In addition, we also recommend (and appreciate) that you track your sessions using the `X-Client-Session` set to something uniqe per session, such as an UUID or something similar. 

The API response contains the header `X-REQUEST-ID`, which is useful if you need to report a problem. 

## Pagination 

The response contains two parts: 
  * A list of the objects, with maximum 1000 elements
  * A metadata-element
  
The metadata describes how many objects satisfying your query _(antall)_, the number returned with this page _(returnert)_, 
the number of elements per page _(sidestørrelse)_ and the link _(href)_ to the next page of data. To fetch all data 
you need to iterate through all pages (following the _href_ link on each page) untill you get an empty page (i.e. the list _objekter_ is empty and the metadata element  _returnert_ = 0. 

```json
{  "objekter": [ { "long" : "list" }, { "of" : "objects" } ], 
  "metadata": {
    "antall": 1001, 
    "returnert": 1000,
    "sidestørrelse": 1000,
    "neste": {
      "start": "AEhZez1g4yb",
      "href": "/api-endpoint?start=AEhZez1g4yb"
    }
  }
}
```
