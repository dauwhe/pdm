# Publication Data Model (PDM)

## Introduction

The publishing world has been making OEBs and EPUBs for twenty years. We have been struggling for four years to write a web publications spec. We  don't have an audiobook spec, but we need one. We wonder what the future of EPUB is. We struggle to fit in with the web world. 


What we actually want are behaviors. We want a page turn at the end of chapter one to automatically take us to chapter two. We don't want to have to press "play" for every track of an audiobook. We want search to work across a whole publication, regardless of how many files. We want a different user interface/display mode when we're reading a publication. We want to bundle up all the pieces of a publication into a single file and send them to our friends, or our distributor. We want to be able to easily create, edit, and understand our publications.

But to do all these things, we need information. We really only need to know three things:

1. where do I start?

2. what comes next?

3. what's part of the publication, and what isn't?

Maybe it's only two things, boundary and sequence. 

I wonder if it's possible to define a flexible data model for publishing, which can serve as common DNA for many different types of publications. With differing serializations and packaging, can we express everything from EPUB 3 to a futuristic web publication with a relatively simple model? Let's try.


**NOTE:** Nothing here extends the capabilities of the web. We need that, but we don't fully know what we need. We need to experiment, but our experimentation would be facilitated by having an agreed-on data model. Here's the basic info about a publication—there may be hundreds of ways of expressing that with markup and script. I suspect we'll run into problems with personalization, with pagination, with crafting URLs that point to secondary browsing contexts. Maybe we'll need HTML imports, or other forms of transclusion. But let's all experiment while sharing the fundamental expressions of sequence and boundary that define a publication. 

## The existing landscape and web application manifest.

This work does not happen in a vacuum. EPUB is a billion-dollar industry. The web is part of the very fabric of our lives. We must reuse existing technology as much as possible.

To a large extent, we are trying to express metadata about a collection of web resources. A collection of web resources might be a web site, or a web application. And there is an existing effort to express metadata about web applications, namely the [Web Application Manifest](https://w3c.github.io/manifest/) spec, which we'll refer to as WAM: 

> This specification defines a JSON-based manifest file that provides developers with a centralized place to put metadata associated with a web application.

More importantly, we find:

> Using this metadata, user agents can provide developers with means to create user experiences that are more comparable to that of a native application.

*This* is close to what we want. We want to provide a better user experience for publications. We might need help from user agents. We definitely need help from developers.

The Publishing Working Group (PWG) has rejected WAM, but we should reconsider that decision.

**NOTE:** Much of what is written below applies to any manifest file, whether it is identified as a web application manifest or not. However, using WAM gives us the tremendous advantage that the processing model and the nature of the display modes is already defined. We do not have the skills inside the Publishing Working Group to define things in a manner consistent with browser architecture and the web security model. 

**NOTE:** WAM largely exists to allow web apps to be "installed" in the manner of native apps on mobile platforms. In an ideal world, we would be able to add publications to bookshelves, as users want to organize their collections. The similarity between these two concepts is interesting. 

## A few words on Metadata, JSON-LD, and schema.org

The design of web publications has been complicated by our desire to use schema.org. We apply unfamiliar names to concepts. We introduce complications (two contexts!). And the end result is that I paste our work into Google's structured data testing tool, and get hundreds of errors. 

We should focus on the structural issues of publications at this early stage. The web has a multiplicity of methods to assign metadata to web pages. Perhaps we can leave this up to authors and developers, or at least not spend much of our time worrying about how titles sort, when we don't know how publications work. 



## Web Publications 

What is a web publication? Right now it's essentially a URL that points to an HTML resource that contains a manifest or a link to a manifest. The web publication manifest is currently defined as JSON-LD with a schema.org context as well as a custom context. Instead, let's just start with a very simple WAM:


```json
{
  "name": "Lysistrata",
  "start_url": "https://publisher.example.com/978000000000X/"
}
```


We can borrow from the work of PWG to add `readingOrder` and `resources`. 

```json
{

  "name": "Lysistrata",
  "start_url": "https://publisher.example.com/978000000000X/",
  "readingOrder": [
    { "href": "chapter-001.html" },
    { "href": "chapter-002.html" },
    { "href": "chapter-003.html" }
  ],
  "resources": [
    { "href": "cover.jpg",
      "type": "image/jpeg",
      "rel": "cover-image" },
    { "href": "nav.html",
      "type": "text/html",
      "rel": "contents" }
  ],
}

```

Ah, now we have information on the name, scope, and sequence of the web publication, and can even find a cover image and a table of contents.


## Audio Books

Can we use a similar data model for audio books? In a packaged audio book, we don't really have a use case for direct display on the web, so we don't need HTML. [Blackstone Audio](https://github.com/blackstoneaudio/audiobook-spec) proposed a simple model using YAML; perhaps that could be slightly rewritten to be not-incompatible with WAM, and use some of the same ideas as WPUB.

I've flattened their structure a bit, and renamed some things. Their full example also includes a lot of additional information that should be expressible in this model, but I've removed to show a simpler example. 

```yaml
type: audiobook
name: "Lysistrata"                                 
author: "Aristophanes"                       
narrator: "various"                            
copyright: "Public Domain"
publisher: "Librevox"                           
id: "978000000000X"                                                                                              
modified: "2018-12-20T16:00:01Z"                                                                                                                         
resources:
  - href: "lysistrata_1207.jpg"
    title: "Lysistrata Cover"
    type: "image/jpeg"
    rel: "cover"
readingOrder:
  - href: "lysistrata_110_64kb.mp3"             
    duration: 3010                                  
    type: "audio/mpeg"                                                                                        
  - href: "lysistrata_210_64kb.mp3"
    duration: 1663
    type: "audio/mpeg"

```

## Digital Sequential Art (Comics/Manga/Bandes Dessinées)

This is an interesting area. Many publications can be expressed purely as a sequence of images, and something analogous to the audio approach would work.

But if you start adding features like panel-to-panel navigation and page transitions, this starts to look much more like web publications, where the full power of the open web platform is required.

```yaml
type: imagebook
name: "Lysistrata: The Graphic Novel"                                 
author: "Aristophanes"                       
narrator: "various"                            
copyright: "Public Domain"                        
id: "9780000000011"                                                                                              
modified: "2018-12-20T16:00:01Z"                                                                                                                         
resources:
  - href: "lysistrata_cover.jpg"
    title: "Lysistrata Cover"
    type: "image/jpeg"
    rel: "cover"
readingOrder:
  - href: "lysistrata_001.jpg"
    type: "image/jpeg"   
  - href: "lysistrata_002.jpg"
    type: "image/jpeg"
  - href: "lysistrata_003.jpg"
    type: "image/jpeg"
  - href: "lysistrata_004.jpg"
    type: "image/jpeg"
  - href: "lysistrata_005.jpg"
    type: "image/jpeg"
  - href: "lysistrata_006.jpg"
    type: "image/jpeg"
  - href: "lysistrata_007.jpg"
    type: "image/jpeg"
  - href: "lysistrata_008.jpg"
    type: "image/jpeg"

```

As with audio, this could be packaged simply with ZIP. And using YAML rather than JSON eases the authoring burden, and leads to a relatively simple format.


## EPUB

This is the most interesting case, as we have a million or so existing documents, and a whole industry, to worry about. Can we map our JSON/YAML to OPF? I think so.


| WPUB/WAM JSON | AUDIO YAML | EPUB OPF XML |
| ------------- | ------------- | ------------- |
| readingOrder | readingOrder | spine |
| readingOrder | readingOrder | spine |
| resources | resources | manifest minus spine |
| name | name | dc:title |
| modified | modified | dc:modified |
| id | id | dc:identifier |
| type | type | media-type |


```xml
<?xml version="1.0" encoding="UTF-8"?>
<package xmlns="http://www.idpf.org/2007/opf" version="3.0" xml:lang="en" unique-identifier="q">
<metadata xmlns:dc="http://purl.org/dc/elements/1.1/">
  <dc:title id="title">Lysistrata</dc:title>
  <dc:language>en</dc:language>
  <dc:identifier id="q">urn:isbn:978000000000X</dc:identifier>
  <meta property="dcterms:modified">2018-12-20T16:00:01Z</meta>
</metadata>
<manifest>
  <item id="chapter-001"  href="chapter-001.xhtml" media-type="application/xhtml+xml"/>
  <item id="chapter-002"  href="chapter-002.xhtml" media-type="application/xhtml+xml"/>
  <item id="chapter-003"  href="chapter-003.xhtml" media-type="application/xhtml+xml"/>
  <item id="cover-image"  href="cover.jpg" media-type="image/jpeg properties="cover-image"/>
  <item id="nav"  href="nav.xhtml" media-type="application/xhtml+xml" properties="nav"/>
</manifest>
<spine>
  <itemref idref="chapter-001" />
  <itemref idref="chapter-002" />
  <itemref idref="chapter-003" />
</spine>
</package>
```



## Entry pages and the HTML question

We mentioned that one of the big questions is, "where do I start?"

 - WAM has `start_url`.

 - A web publication has a URL, although it's not quite clear if you go to that URL and find a manifest with the first item in the reading order being a different URL.

 - EPUB has `container.xml` pointing to an OPF file which contains a first `spine` item.

 - Audiobooks are more purely sequential; it seems that they would just start at the first audio resource. 
 
This seems to imply that an HTML entry page is not needed in the audio case. It is necessary for WPUB.

```html
<link rel="manifest" href="manifest.json">
```

## Conversion

- an unzipped EPUB 3 works relatively well on the web if you can start with the `nav` file. If converting EPUB to WPUB, could you convert OPF to WAM/PDM, and add a link to that manifest to the `nav` file, and identify the location of the `nav` file as the URL of the WPUB?

- WPUB to EPUB would involve creating an OPF file from the manifest

- Audio to WPUB would involve YAML to JSON, and creating an HTML entry page with a link to the new manifest.

- Any conversion to or from EPUB would ignore some less-used features of EPUB, such as multiple renditions, or archaic features like the NCX. We would not aim to preserver internal IDs in the package file.

## Packaging

 - The forthcoming(?) [Web Packaging](https://github.com/WICG/webpackage) spec from WICG will have a dependency on WAM. Having a WPUB manifest based on WAM will help.
 
 - A very simple ZIP + well-known location for manifest (or only one .yaml or .json file) seems to work for audio books, which don't need the additional complexity of OCF.
 
 - EPUB can continue to use OCF for now.
 
## The future of EPUB

EPUB is really three specs:

1. Content documents. What HTML and CSS can I use?

2. OPF. The package describes structural and bibliographic metadata. 

3. OCF. How to package it?

Perhaps the primary differentiation between WPUB and EPUB is packaging?

There's no reason we couldn't update EPUB to use, for example, HTML in addition to XHTML. Existing content would not become invalid.









