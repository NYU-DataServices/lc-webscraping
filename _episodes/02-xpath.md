---
title: "Selecting content on a web page with XPath"
teaching: 30
exercises: 15
questions:
- "How can I select a specific element on web page?"
- "What is XPath and how can I use it?"
objectives:
- "Introduce XPath queries"
- "Explain the structure of an XML or HTML document"
- "Explain how to view the underlying HTML content of a web page in a browser"
- "Explain how to run XPath queries in a browser"
- "Introduce the XPath syntax"
- "Use the XPath syntax to select elements on this web page"
keypoints:
- "XML and HTML are markup languages. They provide structure to documents."
- "XML and HTML documents are made out of nodes, which form a hierarchy."
- "The hierarchy of nodes inside a document is called the node tree."
- "Relationships between nodes are: parent, child, sibling."
- "XPath queries are constructed as paths going up or down the node tree."
- "XPath queries can be run in the browser using the `$x()` function."
---
Before we delve into web scraping proper, we will first spend some time introducing
some of the techniques that are required to indicate exactly what should be
extracted from the web pages we aim to scrape.

The material in this section was adapted from the [XPath and XQuery Tutorial](https://github.com/code4libtoronto/2016-07-28-librarycarpentrylessons/blob/master/xpath-xquery/lesson.md)
written by [Kim Pham](https://github.com/kimpham54) ([@tolloid](https://twitter.com/tolloid))
for the July 2016 [Library Carpentry workshop](https://code4libtoronto.github.io/2016-07-28-librarycarpentry/) in Toronto.

# Introduction
XPath (which stands for XML Path Language) is an _expression language_ used to specify parts of an XML document.
XPath is rarely used on its own, rather it is used within software and languages that are aimed at manipulating
XML documents, such as XSLT, XQuery or the web scraping tools that will be introduced later in this lesson.
XPath can also be used in documents with a structure that is similar to XML, like HTML.

## Markup Languages
XML and HTML are _markup languages_. This means that they use a set of tags or rules to organise and provide
information about the data they contain. This structure helps to automate processing, editing, formatting,
displaying, printing, etc. that information.

XML documents stores data in plain text format. This provides a software- and hardware-independent way of storing,
transporting, and sharing data. XML format is an open format, meant to be software agnostic. You can
open an XML document in any text editor and the data it contains will be shown as it is meant to be represented.
This allows for exchange between incompatible systems and easier conversion of data.


> ## XML and HTML
>
> Note that HTML and XML have a very similar structure, which is why XPath can be used almost interchangeably to
> navigate both HTML and XML documents. In fact, starting with HTML5, HTML documents are fully-formed XML documents.
> In a sense, HTML is like a particular dialect of XML.
>
{: .callout}

XML document follows basic syntax rules:

* An XML document is structured using _nodes_, which include element nodes, attribute nodes and text nodes
* XML element nodes must have an opening and closing tag, e.g. `<catfood>` opening tag and `</catfood>` closing tag
* XML tags are case sensitive, e.g. `<catfood>` does not equal `<catFood>`
* XML elements must be properly nested:

```
<catfood>
  <manufacturer>Purina</manufacturer>
    <address> 12 Cat Way, Boise, Idaho, 21341</address>
  <date>2019-10-01</date>
</catfood>
```
* Text nodes (data) are contained inside the opening and closing tags
* XML attribute nodes contain values that must be quoted, e.g.
``` <catfood type="basic"></catfood> ```

# XPath Expressions

XPath is written using expressions. Expressions consist of values, e.g., 368, and operators, e.g., +, that will return 
a single value. `368 + 275` is an example of an expression. It will return the value `643`. In programming terminology, this is called evaluating, which simply means reducing down to a single value. A single value with no operators, e.g. `35`, can also be called an expression, though it will evaluate only to its existing value, e.g. 35.

Using XPath is similar to using advanced search in a library catalogue, where the structured nature of bibliographic information allows us to specify which metadata fields to query. For example, if we want to find books *about* Shakespeare but not works *by* him, we can limit our search function to the `subject` field only.

When we use XPath, we do not need to know in advance what the data we want looks like (as we would with regular expressions, where we need to know the pattern of the data). Since XML documents are structured into fields called nodes, XPath makes use of that structure to navigate through the nodes to select the data we want. We just need to know in which nodes within an XML file the data we want to find resides. When XPath expressions are evaluated on XML documents, they return objects containing the nodes that you specify. 

## XPath always assumes *structured* data.

Now let's start using XPath.

## Navigating through the HTML node tree using XPath

A popular way to represent the structure of an XML or HTML document is the _node tree_:

![HTML Node Tree](http://www.w3schools.com/js/pic_htmltree.gif)

In an HTML document, everything is a node:

* The entire document is a document node
* Every HTML element is an element node
* The text inside HTML elements are text nodes

The nodes in such a tree have a hierarchical relationship to each other. We use the terms _parent_, _child_ and
_sibling_ to describe these relationships:

* In a node tree, the top node is called the *root* (or *root node*)
* Every node has exactly one *parent*, except the root (which has no parent)
* A node can have zero, one or several *children*
* *Siblings* are nodes with the same parent
* The sequence of connections from node to node is called a *path*

![Node relationships](http://www.w3schools.com/js/pic_navigate.gif)

Paths in XPath are defined using slashes (`/`) to separate the steps in a node connection sequence, much like
URLs or Unix directories.

In XPath, all expressions are evaluated based on a *context node*. The context node is the node in which a path
starts from. The default context is the root node, indicated by a single slash (/), as in the example above.

The most useful path expressions are listed below:

| Expression   | Description |
|-----------------|:-------------|
| ```nodename```| Select all nodes with the name "nodename"   |
| ```/```  | A beginning single slash indicates a select from the root node, subsequent slashes indicate selecting a child node from current node  |
| ```//``` | Select direct and indirect child nodes in the document from the current node - this gives us the ability to "skip levels" |
| ```.```       | Select the current context node   |
|```..```  | Select the parent of the context node|
|```@```  | Select attributes of the context node|
|```[@attribute = 'value']```   |Select nodes with a particular attribute value|
|`text()`| Select the text content of a node|
| &#124;|Pipe chains expressions and brings back results from either expression, think of a set union |


## Navigating through a webpage with XPath using a browser console

We will use the HTML code that describes this very page you are reading as an example. By default, a web browser
interprets the HTML code to determine what markup to apply to the various elements of a document, and the code is
invisible. To make the underlying code visible, all browsers have a function to display the raw HTML content of
a web page.

> ## Display the source of this page
> Using your favourite browser, display the HTML source code of this page.
>
> Tip: in most browsers, all you have to do is do a right-click anywhere on the page and select the "View Page Source"
> option ("Show Page Source" in Safari).
>
> Another tab should open with the raw HTML that makes this page. See if you can locate its various elements, and
> this challenge box in particular.
>
{: .challenge}


> ## Using the Safari browser
>
> If you are using Safari, you must first turn on the "Develop" menu in order to view the page source, and use the
> functions that we will use later in this section. To do so, navigate to Safari > Preferences and in the Advanced tab
> select the "Show Develop in menu bar" option. Note: In recent versions of Safari you must first turn on the "Develop" 
> menu (in Preferences) and then navigate to `Develop > Show Javascript Console` and then click on the "Console" tab.
>
{: .callout}

The HTML structure of the page you are currently reading looks something like this (most text and elements have
been removed for clarity):

~~~
<!doctype html>
<html lang="en">
  <head>
    (...)
    <title>{{page.title}}</title>
  </head>
  <body>
	 (...)
  </body>
</html>
~~~
{: .output}

We can see from the source code that the title of this page is in a `title` element that is itself inside the
`head` element, which is itself inside an `html` element that contains the entire content of the page.

Say we wanted to tell a web scraper to look for the title of this page, we would use this information to indicate the
_path_ the scraper would need to follow at it navigates through the HTML content of the page to reach the `title`
element. XPath allows us to do that.

We can run XPath queries directly from within all major modern browsers, by enabling the built-in JavaScript console.

> ## Display the console in your browser
>
> * In Firefox, use to the *Tools > Web Developer > Web Console* menu item.
> * In Chrome, use the *View > Developer > JavaScript Console* menu item.
> * In Safari, use the *Develop > Show Error Console* menu item. If your Safari browser doesn't have a Develop menu,
>   you must first enable this option in the Preferences, see above.
>
{: .callout}

Here is how the console looks like in the Firefox browser:

![JavaScript console in Firefox]({{ page.root }}/fig/firefox-console.png)

For now, don't worry too much about error messages if you see any in the console when you open it. The console
should display a _prompt_ with a `> ` character (`>>` in Firefox) inviting you to type commands.

The syntax to run an XPath query within the JavaScript console is `$x("XPATH_QUERY")`, for example:

~~~
$x("/html/head/title/text()")
~~~
{: .source}

This should return something similar to

~~~
<- Array [ #text "{{page.title}}" ]
~~~
{: .output}

The output can vary slightly based on the browser you are using. For example in Chrome, you have to "open" the
return object by clicking on it in order to view its contents.

Let's look closer at the XPath query used in the example above: `/html/head/title/text()`. The first `/` indicates
the _root_ of the document. With that query, we told the browser to

|-----------------|:-------------|
| `/`| Start at the root of the document... |
| `html/`| ... navigate to the `html` node ... |
| `head/`| ... then to the `head` node  that's inside it... |
| `title/`| ... then to the `title` node that's inside it... |
| `text()`| and select the text node contained in that element |

Using this syntax, XPath thus allows us to determine the exact _path_ to a node.

> ## Select the "Introduction" title
> Write an XPath query that selects the "Introduction" title above and try running it in the console.
>
> Tip: if a query returns multiple elements, the syntax `element[1]` can be used. Note that
> XPath uses one-based indexing, therefore the first element has index 1, the second has index 2 etc.
>
> > ## Solution
> >
> > ~~~
> > $x("/html/body/div/article/h1[1]")
> > ~~~
> > {: .source}
> >
> > should produce something similar to
> >
> > ~~~
> > <- Array [ <h1#introduction> ]
> > ~~~
> > {: .output}
> >
> {: .solution}
{: .challenge}

Before we look into other
ways to reach a specific HTML node using XPath, let's start by looking closer at how nodes are arranged
within a document and what their relationships with each others are.


For example, to select all the `blockquote` nodes of this page, we can write

~~~
$x("/html/body/div/article/blockquote")
~~~
{: .source}

This produces an array of objects:

~~~
<- Array [ <blockquote.objectives>, <blockquote.callout>, <blockquote.callout>, <blockquote.challenge>, <blockquote.callout>, <blockquote.callout>, <blockquote.challenge>, <blockquote.challenge>, <blockquote.challenge>, <blockquote.keypoints> ]
~~~
{: .output}

This selects all the `blockquote` elements that are under `html/body/div`. If we want instead to select _all_
`blockquote` elements in this document, we can use the `//` syntax instead:

~~~
$x("//blockquote")
~~~
{: .source}

This produces a longer array of objects:

~~~
<- Array [ <blockquote.objectives>, <blockquote.callout>, <blockquote.callout>, <blockquote.challenge>, <blockquote.callout>, <blockquote.callout>, <blockquote.challenge>, <blockquote.solution>, <blockquote.challenge>, <blockquote.solution>, 3 more… ]
~~~
{: .output}

> ## Why is the second array longer?
> If you look closely into the array that is returned by the `$x("//blockquote")` query above,
> you should see that it contains objects like `<blockquote.solution>` that were not
> included in the results of the first query. Why is this so?
>
> Tip: Look at the source code and see how the challenges and solutions elements are
> organised.
>
{: .challenge}

We can use the `class` attribute of certain elements to filter down results. For example, looking
at the list of `blockquote` elements returned by the previous query, and by looking at this page's
source, we can see that the blockquote elements on this page are of different classes
(challenge, solution, callout, etc.).

To refine the above query to get all the `blockquote` elements of the `challenge` class, we can type

~~~
$x("//blockquote[@class='challenge']")
~~~
{: .source}

which returns

~~~
Array [ <blockquote.challenge>, <blockquote.challenge>, <blockquote.challenge>, <blockquote.challenge> ]
~~~
{: .output}


> ## Select the "Introduction" title by ID
> In a previous challenge, we were able to select the "Introduction" title because we knew it was
> the first `h1` element on the page. But what if we didn't know how many such elements were on the
> page. In other words, is there a different attribute that allows us to uniquely identify that title
> element?
>
> Using the path expressions introduced above, rewrite your XPath query to select
> the "Introduction" title without using the `[1]` index notation.
>
> Tips:
>
> * Look at the source of the page or use the "Inspect element" function of your browser to see what
>   other information would enable us to uniquely identify that element.
> * The syntax for selecting an element like `<div id="mytarget">` is `div[@id = 'mytarget']`.
>
>
> > ## Solution
> >
> > ~~~
> > $x("/html/body/div/h1[@id='introduction']")
> > ~~~
> > {: .source}
> >
> > should produce something similar to
> >
> > ~~~
> > <- Array [ <h1#introduction> ]
> > ~~~
> > {: .output}
> >
> {: .solution}
{: .challenge}


# References

* [W3Schools: JavaScript HTML DOM Navigation](http://www.w3schools.com/js/js_htmldom_navigation.asp)
* [XPath Cheatsheet]({{ '/xpath-cheatsheet/index.html' | absolute_url }})
