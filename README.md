AltXML
======

An XML Parser/DOM for Rebol 3. From the REBOL Console:

```rebol
do %r3xml.r
? load-xml
```

Sample Usage
============

To get all the links from an xhtml page:

```rebol
html: load-xml/dom http://w3.org
links: html/get-by-tag <a>
foreach link links [print ["(" link/get #href ")" link/text]]
```

To get entry titles from an RSS feed:

```rebol
rss: load-xml/dom http://www.rebol.com/article/carl-rss.xml
entries: rss/get-by-tag <item>
foreach entry entries [probe entry/get <title>]
```

Today's Weather

```rebol
weather: http://weather.yahooapis.com/forecastrss?p=35205
weather: load-xml/dom weather
weather: pick weather/get-by-tag <condition> 1
probe weather: context [
    temp: rejoin [weather/get #temp "°F"]
    sky: weather/get #text
]
```

load-xml
--------

This function parses an XML document provided by string!, url! or file! producing a representative block. Tags are represented by the tag! type, attributes by the issue! type and text prefixed by a /text refinement.

```rebol
>> load-xml "<a b=\'c\'>D</a>"
== [
    <a> [
        #b "c"
        %.txt "D"
    ]
]
```

Tags with only text as children have a single value:

```rebol
>> load-xml "<a>B</a>"
== [
    <a> "B"
]
```

/dom refinement
===============

Wraps the above document in an object! with accessor functions.  Values from the accessor functions are similarly wrapped in objects.

get-by-tag 'tag
---------------

Returns a block of child nodes where the tag matches the 'tag value.

```rebol
body: doc/get-by-tag <body>
body/get-by-tag <p>
```

get-by-id 'id
-------------

Returns the first matching child node that has an attribute id matching the the `'id` value.

```rebol
header: doc/get-by-id "header"
```

children
--------

Returns a block of child nodes.

```rebol
body/children
```

sibling /before /after
----------------------

Returns an adjacent node or none!

```rebol
header/sibling/after
```

get 'name
---------

Returns the value of an immediate child node of name `'name` (attribute or child tag)

```rebol
doc/get #b
doc/get <a>
```

text
----

Returns the textual value of a tag node.

```rebol
header/text
```

value
-----

The value of a node.

```rebol
header/value
```

flatten
-------

Serializes the document back to an XML string.

path
----

Select nodes and values using a path notation:

```rebol
; all titles in an RSS feed
rss/path [<rss> <channel> <item> <title> ?]

; all item nodes in an XML document
rss/path [* <item>]

; first header in an HTML document
html/path [<html> * <h1> 1]
```

Notation includes:

```rebol
<rss> - select tags at the current depth
#version - select attributes at the current depth
* <item> - select any descendant with this tag
2 - selects the second result  
? - converts the selection(s) to that node's value
```

Not implemented:
================

parent
------

To follow. Will return a node's parent node.