# Kixt Transmissions

## Abstract {#abstract}

The following specification defines a transmission format for documents.
This format has been designed for use with [Unicode] and [Kixt charsets][Kixt charset], although it may conceivably be adopted by other character sets as well.

<nav id="toc" markdown="block">
## Contents

+ Table of Contents
{:toc id="toc.contents"}
</nav>

## 1. Introduction {#introduction}

### 1.1 Purpose and Scope
{: id="introduction.purpose"}

This specification defines the <dfn id="Kixt_Transmission_Format">Kixt Transmission Format</dfn>, a means of encoding texts for transmission and storage.
It is a text‐based format, which means that it is intended for use with sequences of codepoints which are intended to represent characters according to some character set.
It is not suitable for transmitting binary information.

<div role="note" markdown="block">
In essence, the [Kixt Transmission Format] is a container format (like O·G·G or M·P·4), but for plaintext rather than multimedia.
</div>

This specification does not define the *means* of transmitting the codepoints of a [Kixt transmission], only the semantics that those codepoints entail.
It may conceivably be used in conjunction with networking protocols, or with files being read from or saved to disk.

<div role="note" markdown="block">
The file extension `.kixt` or `.kxt` is suggested for saved documents which are encoded according to the [Kixt Transmission Format].
</div>

### 1.2 Relationship to Other Specifications
{: id="introduction.related"}

This document is part of the [Kixt family of specifications][Kixt].
It is also built upon [R·D·F] technologies.

In this document, the following prefixes are used to represent the following strings:

| Prefix | Expansion |
| :--: | --- |
| `kixt:` | `https://spec.go.kibi.network/ns/kixt/#` |

## 2. Character Sets {#character}

A character set defined by a [Kixt Charset Definition] is <dfn id="dfn.transmission-compatible">transmission compatible</dfn> if and only if :—

+ It does not assign the codepoints `FEFF` or `FFFE`.

+ The objects of the [compatibility properties][Kixt compatibility property] are equal to those defined in <https://spec.go.kibi.network/-/kixt-transmissions/charset> for all characters so defined.

The [Unicode] character set, as well as the A·S·C·I·I subset thereof, is assumed to be transmission compatible.
Whether other character sets are transmission compatible is left undefined by this specification.

Outside of a [page][Kixt page], the character set of a [transmission][Kixt transmission] is <https://spec.go.kibi.network/-/kixt-controls/charset>.

In this specification, characters within a character set are identified by codepoint (always listed in hexadecimal).
For purposes of readability, the name used in <https://spec.go.kibi.network/-/kixt-controls/charset> is also provided.
However, there is no requirement that all [transmission‐compatible character sets][transmission‐compatible character set] necessarily use these same names.

A character set is <dfn id="dfn.variable-width-compatible">variable‐width compatible</dfn> if it only defines codepoints in the range `0000`–`FFFF`, and the set of bytes used for the most significant and least significant places of characters are disjoint for all codepoints greater than `0000`.
A character set defined by a [Kixt Charset Definition] is additionally only variable‐width compatible if the value of its `kixt:supportsVariableEncoding` property is `true`.

<div role="note" markdown="block">
The requirement of disjointedness for [variable‐width‐compatible character sets][variable‐width‐compatible character set] means that determining the codepoint a byte belongs to only requires looking at the previous or next byte.
</div>

# 2.1 Control Characters
{: id="character.control"}

The <dfn id="dfn.control_character">control characters</dfn> are, for any [Kixt charset], any [character][Kixt character] with a `kixt:basicType` of `kixt:CONTROL`.
The control characters of other character sets are not defined by this specification.
Many (although not all) [transmission characters][Kixt transmission character] are control characters.

All [control characters][Kixt control character] which are not [transmission characters][Kixt transmission character] are invalid within [documents][Kixt document] and should be replaced with `1A INVALID` on reading.

The following [control characters][Kixt control character] in a [transmission‐compatible character set] are <dfn id="transmission_character">transmission characters</dfn>:

+ `01 HEAD`
+ `02 BEGIN`
+ `03 FINISH`
+ `04 DONE`
+ `16 IDLE`
+ `17 BREAK`
+ `18 CANCEL`
+ `19 END`
+ `1A INVALID`

### 2.2 Overview of Characters
{: id="character.overview"}

The broad meanings and interpretation of the characters whose semantics are defined by this specification are given below.
Note that the specific usage of these characters will be clarified in later sections.

`00 NULL`

: A meaningless [format character][Kixt format character].
  This character may be used for byte‐padding within a source text, and does not represent anything in itself.
  All programs should ignore `00 NULL` characters whenever they appear, treating them as though they were not present.

  <div role="note" markdown="block">
  Of particular importance, programs or algorithms which preform searching or sanitization functions must strip all `00 NULL` characters from their input before processing.
  Failure to do so may allow unsafe syntactic constructs to propagate to places where they should otherwise not be allowed.
  </div>

`01 HEAD`

: Begins a new [header][Kixt header].
  This character is only valid at the beginning of a [page][Kixt page]; in all other situations it should be replaced on reading with `1A INVALID`.

`02 BEGIN`

: Begins a new [text][Kixt text].
  This character is only valid at the beginning of a [page][Kixt page] or to close a [header][Kixt header]; in all other situations it should be replaced on reading with `1A INVALID`.

`03 FINISH`

: Ends a [page][Kixt page].
  Implies a closing of any open [header][Kixt header] or [text][Kixt text].

`04 DONE`

: Ends a [transmission][Kixt transmission].
  Only for use in networked contexts; should not be saved to disk.

`0E LEAVE`

: Opens a [data block][Kixt data block] which effectively switches the character set to [Unicode] until `0F RETURN` is encountered, which closes the block.
  The characters in the [data contents][Kixt data contents] of this block must not be in the range `01`–`1F` or `7F`–`9F`.
  This character is only allowed in [headers][Kixt header]; elsewhere, or if no valid data block can be created, this character should be replaced on reading with `1A INVALID`.

`0F RETURN`

: Closes the [data block][Kixt data block] begun by `0E LEAVE`.
  In all other situations, it should be replaced on reading with `1A INVALID`.

`16 IDLE`

: A meaningless transmission character.
  Unlike `00 NULL`, this character should not be ignored, and is not valid in every location (for example, inside of [headers][Kixt header] or [texts][Kixt text]).
  However, it is given no particular meaning by this specification.

`17 BREAK`

: Ends a [transmission block][Kixt transmission block].
  Only for use in networked contexts; should not be saved to disk.

`18 CANCEL`

: Ends a [transmission][Kixt transmission] by indicating that it was made in error.
  Only for use in networked contexts; should not be saved to disk.

`19 END`

: Closes a [document][Kixt document], signalling that it is complete.

`1A INVALID`

: Replaces an invalid character in a transmission on reading.

`1C VOLUME SEPARATOR`
`1D PART SEPARATOR`
`1E CHAPTER SEPARATOR`
`1F SECTION SEPARATOR`

: [Format characters][Kixt format character] for subdividing a sequence of characters into progressively finer‐grained divisions.
  These have special semantics inside of [headers][Kixt header]; their usage in [texts][Kixt text] is not defined (but not prohibited) by this specification.

`7F NOTHING`

: A meaningless [control character][Kixt control character], used to signal an empty [message][Kixt message].
  Unlike `00 NULL`, this character should not be ignored, and is not valid in every location.
  Furthermore, unlike `16 IDLE`, this is not a [transmission character][Kixt transmission character] and is invalid inside of [documents][Kixt document].
  However, it is given no particular meaning by this specification.

### 2.3 Data Blocks

A <dfn id="dfn.data_block">data block</dfn> is a sequence of codepoints which do not necessarily belong to the character set of the surrounding text.
Every data block begins with one or more <dfn id="dfn.opening_data_character">opening data characters</dfn>, and ends with zero or more <dfn id="dfn.closing_data_character">closing data characters</dfn>.
The <dfn id="dfn.data_contents">data contents</dfn> of a [data block][Kixt data block] are the non–`00 NULL` codepoints between these two sequences.
The only data block defined by this specification is the sequence of codepoints beginning with an opening data character of a valid `0E LEAVE` and ending with a closing data character of a valid `0F RETURN`.
Other specifications may define other data blocks.

For [Kixt charsets][Kixt charset], characters with a `kixt:basicType` of `kixt:DATA` are intended for sole use in data blocks.
These are the <dfn id="dfn.data_character">data characters</dfn>.
The data characters of other character sets are not defined by this specification.

<div role="note" markdown="block">
Although the character sets of [data blocks][Kixt data block] are not necessarily known, [data contents][Kixt data contents] are nevertheless encoded the same as any other characters.
</div>

### 2.4 Messages

A <dfn id="dfn.message">message</dfn> is a sequence of codepoints which performs a similar function to a [control character][Kixt control character].
Each message begins with a single <dfn id="dfn.messaging_character">messaging character</dfn>.
For any [Kixt charset], the messaging characters are those characters with a `kixt:basicType` of `kixt:MESSAGING`.
The messaging characters of other character sets are not defined by this specification.

A [messaging character][Kixt messaging character] may be followed by any of the following, which comprises part of the [message][Kixt message] and determines its <dfn id="dfn.message_contents">message contents</dfn>:

 +  A single codepoint in the range `20`–`7E`.
    In this case, the [message contents][Kixt message contents] are the given codepoint.

 +  A [data block][Kixt data block].
    In this case, the [message contents][Kixt message contents] are the given data block in its entirety.

 +  The codepoint `7F`.
    In this case, the [message contents][Kixt message contents] are empty.

Otherwise, the [message][Kixt message] is invalid and the [messaging character][Kixt messaging character] should be replaced with a `1A INVALID` on reading.

All [messages][Kixt message] are invalid within [documents][Kixt document] and the entirety of any such message should be replaced with a single `1A INVALID` on reading.
The meaning of messages outside of [documents][Kixt document] is not defined by this specification.

### 2.5 Unicode Mappings
{: id="character.mapping"}

For any [Kixt charset], the <dfn id="dfn.Unicode_mapping">Unicode mapping</dfn> of a sequence of codepoints is the sequence of [Unicode] characters which results from :—

1. For each [data character][Kixt data character] which is not part of a [data block][Kixt data block], `U+FFFD`.

2. For `1A INVALID`, `U+FFFD` if it is inside of a [text][Kixt text] or [header][Kixt header], and `U+001A` otherwise.

3. For each character other than `1A INVALID` which is not part of a [data block][Kixt data block], replacing the corresponding assigned [Kixt character] with the codepoints, in order, indicated by the `kixt:unicode` property, or with `U+FFFD` if no character has been assigned.

4. For each [data block][Kixt data block] which begins with `0E LEAVE` and ends with `0F RETURN`, replacing the data block with the codepoints which result from interpreting its [data contents][Kixt data contents] as a sequence of U·T·F‐16 code units (as defined by [Unicode]), interpreting any ill‐formed code unit subsequence as `U+FFFD`.

    <div role="note" markdown="block">
    For clarity :—
    The code units of a [data block][Kixt data block] may in fact be encoded using U·T·F‐8, depending on the encoding form of the transmission.
    The [data contents][Kixt data contents] of a data block are a sequence of 16‐bit codepoints, not the sequence of bytes which represent them in any given encoding form.
    </div>

4. For other [data blocks][Kixt data block], the single character `U+FFFC`, unless specified otherwise by a relevant specification.

For a sequence of Unicode characters, the [Unicode mapping][Kixt Unicode mapping] is the sequence itself.
For characters in other character sets, the Unicode mapping is not defined by this specification.

## 3. Kixt Transmission Format {#model}

### 3.1 Transmissions
{: id="model.transmission"}

A <dfn id="dfn.transmission">Kixt transmission</dfn> is a sequence of bytes conforming to the semantics of this specification.
Transmissions may be read from a file or transmitted over a network.

#### 3.1.1 Transmission encoding
{: id="model.transmission.encoding"}

An <dfn id="dfn.encoding_scheme">encoding scheme</dfn> is a means of representing a sequence of codepoints as a sequence of bytes.
Five possible encoding schemes are defined :—

<dfn id="dfn.Generalized_UTF-8">Generalized U·T·F‐8</dfn>
: As defined by the [W·T·F‐8] specification

<dfn id="dfn.Fullwidth-BE">Fullwidth‐B·E</dfn>
: For Unicode, [U·T·F‐16·B·E][U·T·F‐16].
  For [Kixt charsets][Kixt charset], each codepoint is represented as a sequence of two bytes, with the most significant byte first.

<dfn id="dfn.Fullwidth-LE">Fullwidth‐L·E</dfn>
: For Unicode, [U·T·F-16·L·E][U·T·F‐16].
  For [Kixt charsets][Kixt charset], each codepoint is represented as a sequence of two bytes, with the least significant byte first.

<dfn id="dfn.Variable-BE">Variable‐B·E</dfn>
: For [variable‐width‐compatible character sets][variable‐width‐compatible character set], each codepoint within the [text][Kixt text] of a [document][Kixt document] which is not in a [data block][Kixt data block] is represented as a sequence of either :—

  01. For characters for which one byte is `00`, the other byte.

  02. For all other characters, the codepoint a sequence two bytes, with the most‐significant byte first.

  For all other character sets, outside of [texts][Kixt text], and inside [data blocks][Kixt data block], the same as [Fullwidth‐B·E][Kixt Fullwidth‐B·E].

<dfn id="dfn.Variable-LE">Variable‐L·E</dfn>
: For [variable‐width‐compatible character sets][variable‐width‐compatible character set], each codepoint within the [text][Kixt text] of a [document][Kixt document] which is not in a [data block][Kixt data block] is represented as a sequence of either :—

  01. For characters for which one byte is `00`, the other byte.

  02. For all other characters, the codepoint a sequence two bytes, with the least‐significant byte first.

  For all other character sets, outside of [texts][Kixt text], and inside [data blocks][Kixt data block], the same as [Fullwidth‐L·E][Kixt Fullwidth‐L·E].

<div role="note" markdown="block">
For [transmission‐compatible character sets][transmission‐compatible character set], the `00` byte will always be the most significant byte.
The primary advantage to variable‐width encodings is that they allow A·S·C·I·I texts to be interpreted without modification, while otherwise maintaining a 16‐bit encoding scheme.

It is recommended that the bytes `80–9F` and `E0–FF` be used as most‐significant bytes, and that the characters `A0–DF` be used as least‐significant bytes.
</div>

The [encoding scheme][Kixt encoding scheme] applies to all characters in a [transmission][Kixt transmission], regardless of character set.

If the first two bytes of a [transmission][Kixt transmission] are `FE` followed by `FF`, then the [encoding scheme][Kixt encoding scheme] for the transmission is [Variable‐B·E][Kixt Variable‐B·E].
If the first two bytes of a transmission are `FF` followed by `FE`, then the [encoding scheme][Kixt encoding scheme] for the transmission is [Variable‐L·E][Kixt Variable‐L·E].
In either case, these first two bytes are otherwise ignored.

<div role="note" markdown="block">
In transmissions, the `00 NULL` character is ignored.
This means that any [Fullwidth‐B·E][Kixt Fullwidth‐B·E]‐encoded sequence of characters can safely be interpreted as [Variable‐B·E][Kixt Variable‐B·E], and any [Fullwidth‐L·E][Kixt Fullwidth‐L·E]‐encoded sequence of characters can safely be interpreted as [Variable‐L·E][Kixt Variable‐L·E].
Consequently, separate encoding detection for [Fullwidth‐B·E][Kixt Fullwidth‐B·E] or [Fullwidth‐L·E][Kixt Fullwidth‐L·E] is not required.
</div>

Otherwise, the [encoding scheme][Kixt encoding scheme] is [Generalized U·T·F‐8][Kixt Generalized U·T·F‐8].

#### 3.1.2 Transmission blocks
{: id="model.transmission.block"}

In certain networked situations, a transmission might need to be broken into multiple parts, known as <dfn id="transmission_block">transmission blocks</dfn>.
The character `17 BREAK` may be used to signal the end of a transmission block without implying that a transmission has terminated.

In nonnetwork contexts, or in other contexts where [transmission blocks][Kixt transmission block] are not used, `17 BREAK` should be replaced with `1A INVALID` during reading, unless it is terminal (optionally followed by `04 DONE` or `18 CANCEL`), in which case it should be ignored.

#### 3.1.3 Transmission termination
{: id="model.transmission.termination"}

The end of a transmission may be signalled by the use of `04 DONE` or `18 CANCEL`.
The former of these characters signals a completed transmission, whereas the latter signals that a transmission was made in error and should be discarded.

The end of transmissions should generally only be signalled when communicating over a network.
Nonterminal `04 DONE` and `18 CANCEL` characters which appear in nonnetwork contexts should be replaced with `1A INVALID` during reading.
Terminal `04 DONE` and `18 CANCEL` characters which appear in nonnetwork contexts should be ignored.

### 3.2 Documents
{: id="model.document"}

A <dfn id="dfn.document">document</dfn> is a sequence of bytes which forms a cohesive whole.
Documents may consist of any number of [pages][Kixt page].
A [transmission][Kixt transmission] may consist of multiple documents, or only part of one.

A [document][Kixt document] is automatically opened within any [transmission][Kixt transmission] whenever no document is currently open and a character is encountered which, after reading, is not a [control character][Kixt control character] or part of a [message][Kixt message].
If no document is presently open, a new document may also be explicitly opened by `01 HEAD` or `02 BEGIN`.
Documents are closed by `19 END`.

<div role="note" markdown="block">
The “after reading” stipulation above is meant to indicate that characters which are ignored on reading (`00 NULL`), or which are replaced on reading with `1A INVALID`, do not open a new [document][Kixt document].
</div>

The ends of [documents][Kixt document] should be signalled via `19 END` regardless of context (and even for saved files).
A lack of a `19 END` character indicates that a document is incomplete, perhaps because it was not fully saved, or because a transmission was ended before its end could be reached.

Files saved to disk should only contain a single [document][Kixt document]—and consequently, only a single, terminal `19 END` character.
Programs which concatenate documents should take care to only concatenate the *[pages][Kixt page]* of the documents, and not nonpage content.
When reading a file from disk, a nonterminal `19 END` character should be replaced with `1A INVALID` during reading.

### 3.3 Pages
{: id="model.page"}

A <dfn id="dfn.page">page</dfn> is a section of a [document][Kixt document] consisting of an optional [header][Kixt header], followed by a [text][Kixt text].
Documents may contain any number of pages.
A new page is automatically opened in a document whenever no page is currently open and a character is encountered which, after reading, is not a [transmission character][Kixt transmission character].
Pages can also explicitly be opened with a `01 HEAD` or `02 BEGIN` character.

<div role="note" markdown="block">
[Control characters][Kixt control character] and [messages][Kixt message] have already been replaced with `1A INVALID` at this point, as we are inside of a [document][Kixt document].
</div>

[Pages][Kixt page] extend until the end of their [document][Kixt document], or can be explicitly closed with a `03 FINISH` character (thus allowing the opening of a new page).

#### 3.3.1 Page headers
{: id="model.page.header"}

A <dfn id="dfn.header">header</dfn> provides metadata information about a [page][Kixt page].
It is opened by `01 HEAD` and continues until the start of the [text][Kixt text] (signalled by `02 BEGIN`) or the end of the [page][Kixt page], whichever occurs first.
Headers must precede texts; consequently, a page with a header necessarily must begin with `01 HEAD` (optionally preceded by any number of `16 IDLE`, `1A INVALID`, or `7F NOTHING` characters).

The contents of [headers][Kixt header] define [R·D·F triples][R·D·F triple] whose subject is a node representing the current document.
The predicate and object of each triple is separated by `1D PART SEPARATOR`, and the triples themselves are separated by `1C VOLUME SEPARATOR`.
If no triple can be formed from a sequence of characters so delineated (either because it does not consist of two components separated by a `1D PART SEPARATOR`, contains invalid characters, or does not conform to appropriate [R·D·F] semantics), the entire sequence (up until the next `1C VOLUME SEPARATOR`, or the end of the header) is ignored.

The sequence of characters representing the predicate of the [R·D·F triple] must have a [Unicode mapping][Kixt Unicode mapping] which is a valid [I·R·I].
The sequence of characters representing the object of the RDF triple must have a Unicode mapping which is one of the following:

 +  A valid [I·R·I].

 +  A [literal][R·D·F literal] which does not contain any characters in the range `00-1F` or `7F-9F`, which only contains assigned characters, and which is followed by `1F SECTION SEPARATOR` and a valid [datatype I·R·I][R·D·F datatype I·R·I].

 +  An [R·D·F Collection] whose ordered items are each one of the above, with each item separated by `1E CHAPTER SEPARATOR`.
    Collections cannot be nested.

##### character set

The character set of [headers][Kixt header] is initially <https://spec.go.kibi.family/-/kixt-transmissions/charset>.
The predicate `kixt:charset` can be used to define the character set for all subsequent [R·D·F triples][R·D·F triple]; the object of this predicate is valid if it is an [I·R·I] representing a [transmission‐compatible character set].
If multiple `kixt:charset` predicates with valid objects are declared in a header, all but the first are ignored.

<div role="note" markdown="block">
Because no character set has yet been declared, the use of `0E LEAVE` and `0F RETURN` is required to define the `kixt:charset` of a [header][Kixt header].
This is intentional, as it explicitly forces the character set of a `kixt:charset` declaration to be [Unicode].
</div>

The I·R·I `http://www.unicode.org/versions/latest/` identifies the [Unicode] character set.

<div role="note" markdown="block">
For closely‐related character sets, multiple `kixt:charset` predicates may be used to indicate fallbacks should the preferred character set not be available.
</div>

##### media type

The `kixt:mediaType` predicate can be used to specify the media type of the [text][Kixt text] contents of the [page][Kixt page].
Its value should be an [H·T·T·P media type], as an `xsd:string`.

#### 3.3.2 Page texts
{: id="model.page.text"}

A <dfn id="dfn.text">text</dfn> provides the text contents of a [page][Kixt page].
A new text is automatically opened in a page whenever no [header][Kixt header] is currently open and a character is encountered which, after reading, is not a [transmission character][Kixt transmission character].
Texts can also be opened explicitly with a `02 BEGIN` character.
Texts continue until the end of the page.

If a [page][Kixt page] ends before a [text][Kixt text] is opened, it has an empty text.
The opening and closing `02 BEGIN` and `03 END` characters, if present, are not considered part of a text's contents.

Texts must not contain [transmission characters][Kixt transmission character] other than `1D INVALID`.
Any other transmission characters within a text’s contents should be replaced with `1D INVALID` on reading.

If a `kixt:charset` with a valid object was declared in the [header][Kixt header] to the [page][Kixt page] containing a [text][Kixt text], it provides the character set of the text.
Otherwise, the character set of the text is left to programs to determine.
In the absence of prior knowledge, programs should assume that texts are in [Unicode].

<div role="note" markdown="block">
As a consequence of the above rules, and in the absence of any special configuration or knowledge, a plaintext [document][Kixt document] which contains no [transmission characters][Kixt transmission character] is assumed to consist of a single [page][Kixt page] with a single [Unicode]{::} [text][Kixt text].
</div>

## 4. Changelog {#changelog}

{: id="changelog.2021-12-19"} <time>2021-12-19</time>

: New U·R·L’s and minor revisions.

{: id="changelog.2019-09-11"} <time>2019-09-11</time>

: Clarified the distinction between [transmission characters][Kixt transmission character], [control characters][Kixt control character], and other characters defined in this specification; `7F NOTHING` is no longer a transmission character proper.

: The [Unicode mapping][Kixt Unicode mapping] of `1A INVALID` is now `U+FFFD` inside of [headers][Kixt header] and [texts][Kixt text].

{: id="changelog.2019-09-10"} <time>2019-09-10</time>

: [Transmission characters][Kixt transmission character] now have a `kixt:basicType` of `kixt:CONTROL` and are a subset of [control characters][Kixt control character].

: `7F NOTHING` is now a [transmission character][Kixt transmission character].

: `FFFE` and `FFFF` are now allowed in [data blocks][Kixt data block].

: [Messages][Kixt message] are now formally specified.

: [Data blocks][Kixt data block] which are not part of a [message][Kixt message] can now open a [document][Kixt document].

{: id="changelog.2019-09-05"} <time>2019-09-05</time>

: Clarified that variable‐width encodings are fixed‐width outside of [texts][Kixt text].

: Clarified the U·T·F‐16 interpretation of the [data contents][Kixt data contents] of a `0E LEAVE` [data block][Kixt data block].

: A Kixt charset is now only [variable‐width compatible][variable‐width‐compatible character set] if it has a `true` `kixt:supportsVariableEncoding`.
    This provides charsets with a mechanism for forward‐compatibility guarantees (or not).

{: id="changelog.2019-05-03"} <time>2019-05-03</time>

: Initial specification.

{% include references.md %}
