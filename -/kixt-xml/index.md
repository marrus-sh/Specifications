# Kixt XML

## Abstract {#abstract}

The following specification defines a means of creating [X·M·L documents][X·M·L document] which are useable with [Kixt charsets][Kixt charset].

<nav id="toc" markdown="block">
## Contents

+ Table of Contents
{:toc id="toc.contents"}
</nav>

## 1. Introduction {#introduction}

### 1.1 Purpose and Scope
{: id="introduction.purpose"}

The [Kixt Transmission Format] allows for the association of metadata with [documents][Kixt document] through the use of [headers][Kixt header].
However, using this format to specify character sets in a manner compatible with non‐Kixt‐aware [X·M·L processors][X·M·L processor] is impossible, due to its reliance on characters prohibited in [X·M·L documents][X·M·L document].
This specification defines a subset of the [X·M·L] syntax which may be used to encode texts written in a [Kixt charset].
The resulting document is called a <dfn id="dfn.Kixt_XML_document">Kixt X·M·L document</dfn>.

<div role="note" markdown="block">
The file extension `.xkixt` or `.xkx` is suggested for saved [Kixt X·M·L documents][Kixt X·M·L document].
</div>

### 1.2 Relationship to Other Specifications
{: id="introduction.related"}

This document is part of the [Kixt family of specifications][Kixt].
It is also built upon [X·M·L] and [R·D·F] technologies.

## 2. Character sets {#character}

A [Kixt Charset Definition] is <dfn id="dfn.XML-compatible">X·M·L‐compatible</dfn> if it is [U·T·F‐8 compatible][U·T·F‐8‐compatible character set] and the objects of the [compatibility properties][Kixt compatibility property] are equal to those defined in <https://spec.go.kibi.family/-/kixt-xml/charset> for all characters so defined.

The [Unicode] character set, as well as the A·S·C·I·I subset thereof, is assumed to be [X·M·L compatible][X·M·L‐compatible character set].
Whether other character sets are X·M·L compatible is left undefined by this specification.

The character set of a [Kixt X·M·L document] may be any [X·M·L‐compatible character set].
Kixt XML documents must not be [fully normalized][X·M·L fully normalized], as they do not necessarily contain [Unicode] contents.

### 2.1 Character encoding
{: id="character.encoding"}

[Kixt X·M·L documents][Kixt X·M·L document] must be transmitted as either [Generalized U·T·F‐8][Kixt Generalized U·T·F‐8], [Fullwidth‐B·E][Kixt Fullwidth‐B·E], or [Fullwidth‐L·E][Kixt Fullwidth‐L·E].
Fullwidth‐B·E or Fullwidth‐L·E Kixt X·M·L documents must begin with the codepoint `FEFF`.
Generalized U·T·F‐8 Kixt X·M·L documents may also begin with `FEFF`, but this is not required.

## 3. The Format

### 3.1 Restrictions on the X·M·L syntax
{: id="format.restrictions"}

[Kixt X·M·L documents][Kixt X·M·L document] must follow the syntax defined by [X·M·L], with the additional constraints:

01. [Kixt X·M·L documents][Kixt X·M·L document] must not contain the codepoints `000A`, `000D`, `0085`, or `2028`.

    <div role="note" markdown="block">
    As consequence of this rule, the only valid [X·M·L `<S>`] whitespace in a [Kixt X·M·L document] is `0020`.
    This does not prevent the presence of other, non‐syntactic whitespace, however.
    </div>

02. [Kixt X·M·L documents][Kixt X·M·L document] must not contain any codepoints not defined in <https://spec.go.kibi.family/-/kixt-xml/charset> in any [X·M·L `<Name>`], [`<NCName>`][X·M·L `<NCName>`], [`<Nmtoken>`][X·M·L `<Nmtoken>`], or [`<PubidLiteral>`][X·M·L `<PubidLiteral>`].

03. [Kixt X·M·L documents][Kixt X·M·L document] must not contain an [`<EncodingDecl>`][X·M·L `<EncodingDecl>`] encoding declaration.

04. [Kixt XML documents][Kixt X·M·L document] must not contain any codepoints not assigned in the current character set.

### 3.2 Defining the character set
{: id="format.charset"}

The starting character set for a [Kixt X·M·L document] is [Unicode].

The character set for the [contents][X·M·L content] of any [X·M·L element] can be changed by setting the [attribute][X·M·L attribute] with [local name][X·M·L local name] `charset` and [namespace name][X·M·L namespace name] `https://spec.go.kibi.family/ns/kixt/` on that element.
If the [value][X·M·L attribute value] of this attribute is the [I·R·I] of a supported, [X·M·L‐compatible character set], then this is the character set of the element’s contents.
Otherwise, the character set of the element’s contents is the same as that for its parent, or, in the case of the [root element][X·M·L root], the document as a whole.

## 4. Security {#security}

Using non‐[Unicode] character sets within a document may make scripted [Kixt X·M·L documents][Kixt X·M·L document] more difficult to sanitize.
It is advised that processors of scripted documents which may contain unsafe information fail to recognize *all* character set [I·R·I]s, effectively locking the character set into Unicode, unless the character sets supported by a sanitization filter are known.

## 5. Changelog {#changelog}

{: id="changelog.2021-12-19"} <time>2021-12-19</time>

: New U·R·L’s and minor revisions.

{: id="changelog.2019-09-05"} <time>2019-09-05</time>

: Added [Security](#security) section.

{: id="changelog.2019-05-03"} <time>2019-05-03</time>

: Initial specification.

{% include references.md %}

