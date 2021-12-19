# KIBI Specifications

## Abstract {#abstract}

[KIBI Specifications] are the set of specifications published by [kibigo!] to document various tools she has developed for the web.

<nav id="toc" markdown="block">
##  Contents

+ Table of Contents
{:toc id="toc.contents"}
</nav>

## 1. About KIBI Specifications {#about}

The following specifications are published by [kibigo!] to aid in the comprehension and development of various technologies she has created.
They are provided, without any warranty or guarantee of correctness, under a [🅭🅯🄎 Creative Commons Attribution‐Share·Alike 4·0 International Licence][CC BY-SA 4.0].
Issues can be filed on [GitHub][KIBI Specifications GitHub]

##  2. Kixt  {#kixt}

### 2.1 Introduction
{: id="kixt.introduction"}

Although [Unicode] generally provides an extensive and well‐supported means of storing, classifying, rendering, and transforming text, its status as a universal character set means that minority and experimental scripts may not have a proper representation at a given time.
<dfn id="dfn.Kixt">Kixt</dfn> (<i>KIBI text</i>) aims to provide a limited means for documenting character sets and properties in a simple and clearly‐defined manner.

### 2.2 Specifications
{: id="kixt.specifications"}

[Kixt Charsets]

: Defines the [Kixt Charset Model] and the file format for [Kixt Charset Definitions][Kixt Charset Definition].

[Kixt Transmissions]

: Defines the [Kixt Transmission Format] for storing and communicating [Kixt texts][Kixt text].

[Kixt Formatting and Controls]

: Defines the [Kixt controls], for use communicating with Kixt programs.

[Kixt XML]

: Defines the [Kixt XML document] format for using [Kixt charsets] with XML.

## 3. Changelog {#changelog}

{: id="changelog.2021-12-19"} <time>2021-12-19</time>

: Re·arranged layout; document is no longer Kixt‐specific..

{: id="changelog.2019-09-16"} <time>2019-09-16</time>

: Added link to [Kixt Formatting and Controls].

{: id="changelog.2019-05-01"} <time>2019-05-01</time>

: Initial specification.

{% include references.md %}
