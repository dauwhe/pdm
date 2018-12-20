# Publication Data Model (PDM)

## Introduction

The publishing world has been making EPUBs for a dozen years. We have been struggling for four years to write a web publications spec. We  don't have an audiobook spec, but we need one. We wonder what the future of EPUB is. We struggle to fit in with the web world. 


What we actually want are behaviors. We want a page turn at the end of chapter 1 to automatically take us to page 2. We don't want to press "play" for every track of an audiobook. We want search to work across a whole publication. We want a different user interface/display mode when we're reading a publication. We want to bundle up all the pieces of a publication into a single file and send them to our friends, or our distributor. We want to be able to easily edit and understand our publications.

But to do all these things, we need information. We really only need to know three things:

1. where do I start?

2. what comes next?

3. what's part of the publication, and what isn't?

Maybe it's only two things, boundary and sequence. 

I wonder if it's possible to define a flexible data model for publishing, which can serve as common DNA for many different types of publications. With differing serializations and packaging, can we express everything from EPUB 3 to a futuristic web publication with a relatively simple model? Let's try.

## The existing landscape and web application manifest.

This work does not happen in a vacuum. EPUB is a billion-dollar industry. The web is part of the very fabric of our lives. We must reuse existing technology as much as possible.

To a large extent, we are trying to express metadata about a collection of web resources. A collection of web resources might be a web site, or a web application. And there is an existing effort to express metadata about web applications, namely the web application manifest spec. 

> This specification defines a JSON-based manifest file that provides developers with a centralized place to put metadata associated with a web application.

More importantly, we find

> Using this metadata, user agents can provide developers with means to create user experiences that are more comparable to that of a native application.

*This* is close to what we want. We want to provide a better user experience for publications. We might need help from user agents. We definitely need help from developers.

The PWG rejected WAM, but I think we must reconsider that decision.

## WPUB

What is a web publication? Right now it's essentially a URL that points to an HTML resource that contains a manifest or a link to a manifest.


We can start with WAM:

```json
{
  "name": "To the Lighthouse",
  "start_url": "https://publishing.example.com/978000000000X/",
  "description": "The classic novel by Virginia Woolf",
  "icons": [{
    "src": "images/icon.png",
    "sizes": "192x192"
  }]
}
```


We can borrow from the work of PWG to add `readingOrder` and `resources`. 

```json
{
  "readingOrder": [
    { "href": "chapter-001.html" },
    { "href": "chapter-002.html" },
    { "href": "chapter-003.html" }
  ],
  "resources": [
    { "href": "cover.jpg",
      "type": "image/jpeg",
      "rel": "cover-image" }
  ],
}

```

## Audio Books

Perhaps more interestingly, can we use a similar data model for audio books? In a packaged audio book, we don't really have a use case for direct display on the web, so we don't need HTML. [Blackstone Audio](https://github.com/blackstoneaudio/audiobook-spec) proposed a simple model using YAML; perhaps that could be slightly rewritten to be not-incompatible with WAM, and use some of the same ideas as WPUB.

I've flattened their structure a bit, and renamed some things. Their full example also includes a lot of additional information that should be expressible in this model, but I've removed to show a simpler example. 

```yaml
type: audiobook
name: "To The Lighthouse"                                 
author: "Virginia Woolf"                       
narrator: "Doris Grau"                                 
copyright: "Blackstone Publishing, Inc."                            
id: "9781234567890"                                                                                              
modified: "2012-08-14T16:49:59+00:00"                                                                                                                         
resources:
  - href: "cover.jpg"
    title: "To The Lighthouse Cover"
    type: "image/jpeg"
    rel: "cover"
readingOrder:
  - href: "6729-001.m4a"             
    duration: 3336.3330416666668                                  
    type: "audio/mpeg"                                                                                        
  - href: "6729-002.m4a"
    duration: 2446.1845
    type: "audio/mpeg"
  - href: "6729-003.m4a"
    duration: 2543.1249166666666
    type: "audio/mpeg"
  - href: "6729-004.m4a"
    duration: 3364.022875
    type: "audio/mpeg"
  - href: "6729-005.m4a"
    duration: 3364.022875
    type: "audio/mpeg"

```

## EPUB

This is the most interesting case, as we have an installed base to worry about. Can we map our JSON/YAML to OPF?


| WPUB/WAM JSON | AUDIO YAML | EPUB OPF XML |
| ------------- | ------------- | ------------- |
| readingOrder | readingOrder | spine |
| readingOrder | readingOrder | spine |
| resources | resources | manifest minus spine |
| name | name | dc:title |
| modified | modified | dc:modified |
| id | id | dc:identifier |
| type | type | media-type |


## Entry pages and the HTML question

We mentioned that one of the big questions is, "where do I start?"

 - WAM has `start_url`.

 - A web publication has a URL, although it's not quite clear if you go to that URL and find a manifest with the first item in the reading order being a different URL.

 - EPUB has container.xml pointing to an OPF file which contains a first spine item.

 - Audiobooks are more purely sequential; it seems that they would just start at the first audio resource. 
 
This seems to imply that an HTML entry page is not needed in the audio case. It is necessary for WPUB.

```html
<link rel="manifest" href="manifest.json">
```

## Conversion

- an unzipped EPUB 3 works relatively well on the web if you can start with the `nav` file. If converting EPUB to WPUB, could you convert OPF to WAM/PDM, and add a link to that manifest to the `nav` file, and identify the location of the `nav` file as the URL of the WPUB?

- WPUB to EPUB would involve creating an OPF file from the manifest

- Audio to WPUB would involve YAML to JSON, and creating an HTML entry page with a link to the new manifest.

## Packaging

 - The forthcoming? web packaging from google will have a dependency on WAM. Having a WPUB manifest based on WAM will help.
 
 - A very simple ZIP + well-known location for manifest (or only one .yaml or .json file) seems to work for audio books, which don't need the additional complexity of OCF.
 
 - EPUB can continue to use OCF for now.
 
## The future of EPUB

EPUB is really three specs:

1. Content documents. What HTML and CSS can I use?

2. OPF. The package describes structural and bibliographic metadata. 

3. OCF. How to package it?

Perhaps the primary differentiation between WPUB and EPUB is packaging?

There's no reason we couldn't update EPUB to use, for example, HTML in addition to XHTML. Existing content would not become invalid.









