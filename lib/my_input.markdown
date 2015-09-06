---
layout: page
title: Chisel
---

## Before You Begin

This project is going to center on Markdown and HTML. If you don't have
experience with those two technologies it'll be hard to really understand how
to construct/implement your code.

If you'd like to build just enough familiarity, try these resource:

* [Codecademy's HTML and CSS Track](https://www.codecademy.com/tracks/web) focusing on the "HTML Basics" all the way up to but not including "CSS: An Overview"
* This [Markdown Tutorial](http://markdowntutorial.com/)

## Introduction

HTML is an amazing tool for marking up documents, but it's not very fun for
writing content.

Several years ago, John Gruber proposed [the idea of Markdown](http://daringfireball.net/projects/markdown/syntax). It's a style of
text formatting that's less obtrusive than writing HTML, is easy to remember,
and is highly readable even when not converted to HTML. Here's an example:

```markdown
# My Life in Desserts

## Chapter 1: The Beginning

"You just *have* to try the cheesecake," he said. "Ever since it appeared in
**Food & Wine** this place has been packed every night."
```

A typical user writes a *markdown document*, but here your challenge is to
write a *markdown parser*.

### Experimenting with Markdown

There are markdown parsers available for just about every language you can
imagine. In the Ruby world some of the best known are Redcarpet, Rdiscount, and
RedCloth.

Let's experiment with RedCarpet. Start by installing the gem:

```
$ gem install redcarpet
```

Then let's start IRB and load the gem:

```
$ irb
> require 'redcarpet'
```

Many rendering engines are...over engineered. They are built to support multiple "rendering engines" with the
idea that you might want to output things other than HTML. So with RedCarpet you need to initialize a *renderer*:

```ruby
renderer = Redcarpet::Render::HTML.new
```

Then we take that renderer and connect it with the Markdown engine:

```ruby
engine = Redcarpet::Markdown.new(renderer)
```

Now we finally have a `Markdown` instance stored in the `engine` variable. Now you're ready to render markdown into
HTML:

```ruby
> engine.render("*hello* world")
 => "<p><em>hello</em> world</p>\n"
```

Why did we get that result? We called the `render` method. It's expecting to take in markdown. How does it interpret that
input?

* The string has no new lines, so it's one "block"
* The block doesn't start with `#`, so it's a paragraph (`<p>`) not a header. The whole output will be wrapped in
`<p>` and `</p>`
* Part of the input, `*hello*`, is wrapped in asterisks. That's the Markdown marker for emphasis. So that fragment `hello`
will be wrapped in `<em>` and `</em>`

Let's consider a more complex example. A way, way more complex example. Parse this document:

```ruby
> document = 1 # Fetch the document over http
> document[0..100]  # Get a sense of the content
> engine.render(document)[0..150]  # See some of the rendered output
```

Can you build up a parser like that? Let's find out!

## Learning Goals / Areas of Focus

* Practice breaking a program into logical components
* Test components in isolation and in combination
* Apply Enumerable techniques in a real context
* Read text from and write text to files

## Base Expectations

### An Interaction Model

We're going to use Chisel from the command line, reading in Markdown
files and writing out HTML. It'll go like this:

```bash
$ ruby ./lib/chisel.rb my_input.markdown my_output.html
Converted my_input.markdown (6 lines) to my_output.html (8 lines)
```

Where `my_input.markdown` is a file like this:

```markdown
# My Life in Desserts

## Chapter 1: The Beginning

"You just *have* to try the cheesecake," he said. "Ever since it appeared in
**Food & Wine** this place has been packed every night."
```

And the resulting `my_output.html` would contain the following:

```html
<h1>My Life in Desserts</h1>

<h2>Chapter 1: The Beginning</h2>

<p>
  "You just <em>have</em> to try the cheesecake," he said. "Ever since it appeared in
  <strong>Food &amp; Wine</strong> this place has been packed every night."
</p>
```

Got it?

### Restrictions

* Don't use any regular expressions
* Only use existing parsers to generate sample output or to validate your output

### Expected Functionality

Parsing Markdown is a good application of spiraling design. As such, your
expected functionality is broken down into levels. But *all* of these
levels should be completed in order to earn full marks.

#### Level 1 - Text Basics

A chunk of text is defined as one or more lines of content which does not
contain any blank lines. For example, this is one chunk of text:

```markdown
This is the first line of the paragraph.
This is the second line of the same paragraph.
```

While these are two chunks of text:

```markdown
This is the first line of the first paragraph.

This is the first line of the second paragraph.
```

Build up your Chisel so it supports:

* A chunk of text starting with `#`, `##`, `###`, `####`, or `#####` is turned
into an HTML header (`<h1>`, `<h2>`) with the header level corresponding to the
number of `#` symbols
* A chunk of text *not* starting with `#` is turned into a paragraph

#### Level 2 - Formatting

With Level 1 completed, move on to Level 2:

* Within either a header or a paragraph, any word or words wrapped in `*` should
be enclosed in `<em>` tags
* Within either a header or a paragraph, any word or words wrapped in `**` should
be enclosed in `<strong>` tags

Make sure to consider scenarios like this: `My *emphasized and **stronged** text* is awesome.`

#### Level 3 - Lists

Often in writing we want to create unordered (bullet) or ordered (numbered) lists.
Build support for **unordered lists** like this:

```markdown
My favorite cuisines are:

* Sushi
* Barbeque
* Mexican
```

Which should output:

```html
<p>
  My favorite cuisines are:
</p>

<ul>
  <li>Sushi</li>
  <li>Barbeque</li>
  <li>Mexican</li>
</ul>
```

Then build support for **ordered lists** which use numbers for the markers.
Though, confusingly, the numbers themselves don't matter. Some authors use `1.`
to mark every list element and let the HTML renderer (aka browser) figure things out:

```markdown
My favorite cuisines are:

1. Sushi
2. Barbeque
3. Mexican
```

Which is turned into:

```html
<p>
  My favorite cuisines are:
</p>

<ol>
  <li>Sushi</li>
  <li>Barbeque</li>
  <li>Mexican</li>
</ol>
```

## Extensions

If you finish all the base expectations, consider implementing two of these extensions:

### Images

Add support for images, both with and without the optional title attribute. Don't implement the Reference-Style Links. [See the specification](http://daringfireball.net/projects/markdown/syntax#img)

### Blocks & Blockquotes

Add support for both [Blockquotes](http://daringfireball.net/projects/markdown/syntax#blockquote) and [Code Blocks](http://daringfireball.net/projects/markdown/syntax#precode).

### Links

At this point you're familiar with the basics of how Markdown works. Go
[straight to the source to see how HTML links should work](http://daringfireball.net/projects/markdown/syntax#link). You do not need to implement the "Reference-Style Links", just the normal inline ones.

### Reference-Style Links

Revisit the documentation about Links and build up support for the reference-style links it describes.

### Reverse Chisel

Can you implement a reverser which takes in HTML and outputs Markdown?

This extension is quite hard, so it counts double.

## Evaluation Rubric

The project will be assessed with the following rubric:

### 1. Fundamental Ruby & Style

* 4:  Application demonstrates excellent knowledge of Ruby syntax, style, and refactoring
* 3:  Application shows some effort toward organization but still has 6 or fewer long methods (> 8 lines) and needs some refactoring.
* 2:  Application runs but the code has many long methods (>8 lines) and needs significant refactoring
* 1:  Application generates syntax error or crashes during execution

### 2. Test-Driven Development

* 4: Application is broken into components which are well tested in both isolation and integration
* 3: Application uses tests to exercise core functionality, but has some gaps in coverage or leaves edge cases untested.
* 2: Application tests some components but has many gaps in coverage.
* 1: Application does not demonstrate strong use of TDD

### 3. Functionality

* 4: Application implements all base functionality and two extensions
* 3: Application implements all base functionality
* 2: Application is missing or has issues with one component of the base expectations
* 1: Application has issues with more than one component of base expectations

### 4. Breaking Logic into Components

* 4: Application effectively breaks logical components apart with clear intent and usage
* 3: Application has multiple components with defined responsibilities but there is some leaking of responsibilities
* 2: Application has some logical components but divisions of responsibility are inconsistent or unclear and/or there is a "God" object taking too much responsibility
* 1: Application logic shows poor decomposition with too much logic mashed together
