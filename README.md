# sitequery
*A reactive framework for asynchronous web crawling.*

## Overview
`sitequery` is a [reactive](http://en.wikipedia.org/wiki/Reactive_programming) webcrawling framework that enables 
web crawling through server-side execution of [jQuery selectors](http://api.jquery.com/category/selectors/). `sitequery`
uses [rx.js](http://channel9.msdn.com/Blogs/Charles/Introducing-RxJS-Reactive-Extensions-for-JavaScript) to 
model crawls as async sequence of pages that map to a async sequence of jQuery selected page elements.

## Installation

### npm install
[sudo] npm install sitequery

## Features
`sitequery` has two main abstractions `SiteCrawl` and `SiteQuery` which provide the following features:

  - Web crawls can be paramerized to only go *n* levels deep
  - Configurable crawl timeout                                               
  - Web crawls use a redis store to track visitation and insure a web crawl is cycle-free (no web page is crawled more than once for a given `SiteCrawl` instance)
  - Any valid jQuery selector can be executed across an entire website (web crawl sequence)
  - Support for the latest jQuery version

## Usage

### Crawling a website using SiteCrawl observable
*(From: /examples/hello-crawl.js)*

Allows you to crawl to a depth of *n* into a website

```javascript
var SiteCrawl = require('../lib/sitecrawl').SiteCrawl;

// create a new SiteCrawl of depth 2 with a delay of 1s between next page
// Note: Webcrawling is delayed and will not be executed
// until Subscription
var siteCrawl = new SiteCrawl('http://loku.com',  2, 1000);

// ask for the observable sequence and subscribe for the CrawlResult(s)
siteCrawl.toObservable().Subscribe(function(crawlResult) {                 
  console.log(crawlResult.crawlLink.url.href);
},
// on err
function(exn){
  console.log('Ooo dem Dukes...with exception:' + exn);
},
// on crawl complete
function(){
  console.log('SiteCrawl complete');
});
```

### Executing a jQuery selector on a site using SiteQuery observable
*(From: /examples/hello-query.js)*

Execute jQuery selector to a depth of *n* on a website

```javascript
var SiteQuery = require('../lib/sitequery').SiteQuery;

// create a new SiteQuery of depth 2 with a delay of 1s between next page crawl
// selecting for `img` elements on each page
// Note: Webcrawling is delayed and will not be executed
// until Subscription
var siteQuery = new SiteQuery('http://loku.com', 2, 1000, 'img');

// ask for the observable sequence and subscribe for selected jQuery element(s)
siteQuery.toObservable().Subscribe(function(elem) {
// output the img src                 
  console.log(elem.attr('src'));
},
// on err
function(exn) {
  console.log('Something blowd up with exception:' + exn);
},
// on crawl complete
function() {
  console.log('SiteQuery complete');
});
```

## Credits
  - [David Trejo](http://blog.dtrejo.com/scraping-made-easy-with-jquery-and-selectorga) for original jQuery web crawler concept
  - [Matt Podwysocki](http://codebetter.com/matthewpodwysocki/2010/02/16/introduction-to-the-reactive-extensions-to-javascript/) for detailed examples and documentation of rx.js
  - [Jeremy Van Gogh](http://blogs.msdn.com/b/jeffva/archive/2010/04/14/node-js-bindings-for-rxjs.aspx) the original project lead for rx.js

## ToDo
  - Make practical
  - ~~Obey robots.txt~~
  - Performance profiling
  - Experiment with [jquip](https://github.com/mythz/jquip)
  - Add post-processing backends
  - Graceful errors/degradation for incomplete data/dom for `SiteCrawl` 
  - Handling parse errors more gracefully 
  - Simulate auth into the target site when necessary
  - Parallelisation of requests through rx.js ForkJoin
  - Add asynchronous parsing 
  - Distribute high-cost computation
  - Make crawl selection plugable
  - Add code to term drain node.js Event queue to insure process termination

## License ##

    Copyright (c) Loku. All rights reserved. The use and
    distribution terms for this software are covered by the Eclipse
    Public License 1.0 (http://opensource.org/licenses/eclipse-1.0.php)
    which can be found in the file epl-v10.html at the root of this
    distribution. By using this software in any fashion, you are
    agreeing to be bound by the terms of this license. You must
    not remove this notice, or any other, from this software.
