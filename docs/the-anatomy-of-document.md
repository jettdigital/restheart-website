---
layout: doc-page-md
title:  "The anatomy of a document"
permalink: /docs/the-anatomy-of-a-document.html
class2: active
intro: true
---

RESTHeart maps MongoDB resources (dbs, collections, indexes and documents) to URIs and, following the REST mantra, you can _transfer_ their _states_ back and forth by the means of _representations_.
This section introduces you to such **representation** format.
j
<!-- more -->


## Before we get into the code
{: .post}

RESTHeart uses the [HAL+json](http://stateless.co/hal_specification.html)  hypermedia format. HAL stands for _Hypermedia Application Language_ and it is simple, elegant and powerful.

It builds up on 2 simple concepts: _Resources_ and _Links_

> Resources have state (plain JSON), embedded resources and links


> Links have target (href URI) and relations (aka _rel_)

**That's it!**

![](http://stateless.co/info-model.png)

## The code baby!
{: .post}

We'll get a collection resource and analyze it. As any other in RESTHeart, a collection resource is represented with HAL; thus has its own properties, documents as embedded resources and links (for pagination, sorting, etc).

{% highlight bash %}
$ http --pretty=none GET 127.0.0.1:8080/myfirstdb/myfirstcoll?count
HTTP/1.1 200 OK
Content-Encoding: gzip
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Type: application/hal+json
Content-Length: 564
Date: Tue, 18 Nov 2014 17:39:35 GMT
{% endhighlight %}
{% highlight json %}
{ "_type" : "COLLECTION" , "_id" : "myfirstcoll" , "desc" : "this is my first collection created with restheart" , "_created_on" : "2014-11-18T11:28:27Z" , "_etag" : "546b2d5bef863f5121fc91ef" , "_lastupdated_on" : "2014-11-18T11:28:27Z" , "_collection-props-cached" : false , "_size" : 2 , "_total_pages" : 1 , "_returned" : 2 , "_embedded" : { "rh:doc" : [ { "_type" : "DOCUMENT" , "_id" : "546b2dacef863f5121fc91f1" , "name" : "restheart" , "rating" : "super cool" , "_etag" : "546b2dacef863f5121fc91f0" , "_created_on" : "2014-11-18T11:29:48Z" , "_lastupdated_on" : "2014-11-18T11:29:48Z" , "_links" : { "self" : { "href" : "/myfirstdb/myfirstcoll/546b2dacef863f5121fc91f1"} , "rh:coll" : { "href" : "/myfirstdb"} , "curies" : [ { "href" : "/_doc/?ln=http://www.restheart.org/docs/v0.9/%23api/doc/{rel}" , "templated" : true , "name" : "rh"}]}} , { "_type" : "DOCUMENT" , "_id" : "546b2dcdef863f5121fc91f3" , "name" : "mongodb" , "rating" : "hyper cool" , "_etag" : "546b2dcdef863f5121fc91f2" , "_created_on" : "2014-11-18T11:30:21Z" , "_lastupdated_on" : "2014-11-18T11:30:21Z" , "_links" : { "self" : { "href" : "/myfirstdb/myfirstcoll/546b2dcdef863f5121fc91f3"} , "rh:coll" : { "href" : "/myfirstdb"} , "curies" : [ { "href" : "/_doc/?ln=http://www.restheart.org/docs/v0.9/%23api/doc/{rel}" , "templated" : true , "name" : "rh"}]}}]} , "_links" : { "self" : { "href" : "/myfirstdb/myfirstcoll?count"} , "first" : { "href" : "/myfirstdb/myfirstcoll?pagesize=100&count"} , "last" : { "href" : "/myfirstdb/myfirstcoll&pagesize=100&count"} , "rh:db" : { "href" : "/myfirstdb"} , "rh:filter" : { "href" : "/myfirstdb/myfirstcoll/{?filter}" , "templated" : true} , "rh:sort" : { "href" : "/myfirstdb/myfirstcoll/{?sort_by}" , "templated" : true} , "rh:paging" : { "href" : "/myfirstdb/myfirstcoll/{?page}{&pagesize}" , "templated" : true} , "rh:countandpaging" : { "href" : "/myfirstdb/myfirstcoll/{?page}{&pagesize}&count" , "templated" : true} , "rh:_indexes" : { "href" : "/myfirstdb/myfirstcoll/_indexes"} , "curies" : [ { "href" : "/_doc/?ln=http://www.restheart.org/docs/v0.9/%23api/coll/{rel}" , "templated" : true , "name" : "rh"}]}}

{% endhighlight %}

## The properties
{: .post}

In this case, the collection properties comprise the field _desc_; this is user defined.

The other fields are reserved properties (i.e. are managed automatically by RESTHeart for you); these always starts with _:

| Property                     | Description                                                                                                                                                                                                                           |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **_type**                    | the type of this resource. in this case 'COLLECTION'                                                                                                                                                                                                                                     |
| **_id**                      | the name of the collection                                                                                                                                                                                                            |
| **_etag**                    | entity tag, used for caching and to avoid ghost writes.                                                                                                                                                                               |
| **_created_on**              | collection creation date stamp in ISO 6801 format                                                                                                                                                                                     |
| **_lastupdated_on**          | collection last update date stamp in ISO 6801 format                                                                                                                                                                                  |
| **_size**                    | the number of the documents in the collection (note this is only returned if the count query parameter is specified)                                                                                                                  |
| **_returned**                | the number of the documents embedded in this representation                                                                                                                                                                           |
| **_collection-props-cached** | a flag that gets true when the returned collection properties come from the local cache. refer to the [local cache documentation](http://www.restheart.org/docs/v0.9/configuration.html) for more information |
                                                                                                                                                                                                                                |

{% highlight json %}
{
    "_type": "COLLECTION",
  	"_id": "myfirstcoll",
  	"desc": "this is my first collection created with restheart",
  	"_created_on": "2014-11-18T11:28:27Z",
  	"_etag": "546b2d5bef863f5121fc91ef",
  	"_lastupdated_on": "2014-11-18T11:28:27Z",
  	"_collection-props-cached": false,
  	"_size": 2,
  	"_returned": 2,
    "_embedded": {...},
    "_links": {...}
}
{% endhighlight %}

## The embedded resources
{: .post}

> the collection embedded resources are the collection documents, recursively represented as HAL documents.

{% highlight json %}

	"_embedded": {
        "rh:doc": [
            {
            	"_type": "DOCUMENT", 
                "name": "restheart", 
                "rating": "super cool",
                "_created_on": "2014-11-18T11:29:48Z", 
                "_etag": "546b2dacef863f5121fc91f0", 
                "_id": "546b2dacef863f5121fc91f1", 
                "_lastupdated_on": "2014-11-18T11:29:48Z", 
                "_links": {...}  
            }, 
            {
            	"_type": "DOCUMENT", 
                "name": "mongodb", 
                "rating": "hyper cool"
                "_created_on": "2014-11-18T11:30:21Z", 
                "_etag": "546b2dcdef863f5121fc91f2", 
                "_id": "546b2dcdef863f5121fc91f3", 
                "_lastupdated_on": "2014-11-18T11:30:21Z", 
                "_links": {...}
            }
        ]
    }
{% endhighlight %}

## The links
{: .post}

| Link                  | Description                                                                               |
|-----------------------|-------------------------------------------------------------------------------------------|
| **self**              | link to itself                                                                            |
| **first**             | link to the first page                                                                    |
| **last**              | link to the last page                                                                     |
| **rh:filter**         | templated link for filtering                                                              |
| **rh:sort**           | templated link for sorting                                                                |
| **rh:indexes**        | link to the collection indexes resource                                                   |
| **rh:paging**         | templated link for paging                                                                 |
| **rh:countandpaging** | - templated link for counting (return the number of documents in the collection) and paging|
| **curies**            | (compacts URIes) bind links to documentation. you can figure out that links with rel starting with _rh_ are documented on the www.restheart.org website. For instance the rh:db rel is documented at [http://www.restheart.org/docs/v0.9/#api-coll-db](http://www.restheart.org/docs/v0.9/#api-coll-db). Try it if you like!|


{% highlight json %}
"_links": {
    "curies": [
        {
            "href": "http://www.restheart.org/docs/v0.9/#api-coll-db", 
            "name": "rh", 
            "templated": true
        }
    ], 
    "first": {
        "href": "/myfirstdb/myfirstcoll?pagesize=100&count"
    }, 
    "last": {
        "href": "/myfirstdb/myfirstcoll&pagesize=100&count"
    }, 
    "rh:_indexes": {
        "href": "/myfirstdb/myfirstcoll/_indexes"
    }, 
    "rh:countandpaging": {
        "href": "/myfirstdb/myfirstcoll/{?page}{&pagesize}&count", 
        "templated": true
    }, 
    "rh:db": {
        "href": "/myfirstdb"
    }, 
    "rh:filter": {
        "href": "/myfirstdb/myfirstcoll/{?filter}", 
        "templated": true
    }, 
    "rh:paging": {
        "href": "/myfirstdb/myfirstcoll/{?page}{&pagesize}", 
        "templated": true
    }, 
    "rh:sort": {
        "href": "/myfirstdb/myfirstcoll/{?sort_by}", 
        "templated": true
    }, 
    "self": {
        "href": "/myfirstdb/myfirstcoll?count"
    }
{% endhighlight %}