# omast

**O**rg**M**ode **A**bstract **S**yntax **T**ree.

***

**omast** is a specification for representing orgmode in a [syntax
tree][syntax-tree].
It implements **[unist][]**.

This document may not be released.
See [releases][] for released documents.

## Contents

- [omast](#omast)
  - [Contents](#contents)
  - [Introduction](#introduction)
    - [Where this specification fits](#where-this-specification-fits)
  - [Nodes](#nodes)
    - [`Parent`](#parent)
    - [`Literal`](#literal)
    - [`Root`](#root)
    - [`Element`](#element)
    - [`GreaterElement`](#greaterelement)
    - [`Lesser Element`](#lesser-element)
    - [`Object`](#object)
    - [`Heading`](#heading)
    - [`Section`](#section)
    - [`Center Block`](#center-block)
    - [`Drawer`](#drawer)
    - [`Property Drawer`](#property-drawer)
    - [`Dynamic Block`](#dynamic-block)
    - [`Footnote Definitions`](#footnote-definitions)
    - [`Inline Task`](#inline-task)
    - [`List Item`](#list-item)
    - [`Plain List`](#plain-list)
    - [`Table`](#table)
    - [`Comment Block`](#comment-block)
    - [`Example Block`](#example-block)
    - [`Export Block`](#export-block)
    - [`Source Block`](#source-block)
    - [`Verse Block`](#verse-block)
    - [`Clock`](#clock)
    - [`Diary Sexp`](#diary-sexp)
    - [`Planning`](#planning)
    - [`Comment`](#comment)
    - [`Fixed Width`](#fixed-width)
    - [`Horizontal Rule`](#horizontal-rule)
    - [`Keyword`](#keyword)
    - [`Babel Call`](#babel-call)
    - [`Affiliated Keyword`](#affiliated-keyword)
    - [`LaTeX Environment`](#latex-environment)
    - [`Node Property`](#node-property)
    - [`Table Row`](#table-row)
    - [`Entity`](#entity)
    - [`LaTeX Fragment`](#latex-fragment)
    - [`Export Snippet`](#export-snippet)
    - [`Footnote Reference`](#footnote-reference)
    - [`Citation`](#citation)
    - [`Citation Reference`](#citation-reference)
    - [`Inline BabelCall`](#inline-babelcall)
    - [`Inline SrcBlock`](#inline-srcblock)
    - [`Line Break`](#line-break)
    - [`Link`](#link)
      - [`Radio Link`](#radio-link)
      - [`Plain link`](#plain-link)
      - [`Angle Link`](#angle-link)
      - [`Regular Link`](#regular-link)
    - [Macro](#macro)
    - [Target](#target)
    - [Statistic Cookie](#statistic-cookie)
    - [Subscript](#subscript)
    - [Superscript](#superscript)
    - [Table Cell](#table-cell)
    - [Timestamp](#timestamp)
    - [`Paragraph`](#paragraph)
    - [`Text`](#text)
    - [`Text Markup`](#text-markup)
  - [`Mixin`](#mixin)
    - [`Resource`](#resource)
      - [File](#file)
      - [Protocol](#protocol)
      - [ID](#id)
      - [Custom ID](#custom-id)
      - [Code Ref](#code-ref)
      - [Fuzzy](#fuzzy)
  - [Glossary](#glossary)
  - [References](#references)
  - [Related](#related)
  - [Contribute](#contribute)
  - [License](#license)

## Introduction

This document defines a format for representing [orgmode][] as an [abstract
syntax tree][syntax-tree].

This specification is written in a [Web IDL][webidl]-like grammar.

### Where this specification fits

omast extends [unist][], a format for syntax trees, to benefit from its ecosystem of utilities.

omast is not limited to Dart and can be used in other programming languages.

## Nodes

### `Parent`

```idl
interface Parent <: UnistParent {
  children: [Element | Object]
}
```

**Parent** ([UnistParent][dfn-unist-parent]) represents an abstract interface in omast containing other nodes (said to be children).
Its content is limited to only either Element or Object.

### `Literal`

```
interface Literal <: UnistLiteral {
  value: string
}
```

**Literal** ([**UnistLiteral**][dfn-unist-literal]) represents an abstract interface in omast containing a value.

Its `value` field is a `string`.

### `Root`
Root (Parent) represents a document.

```idl
interface Root <: Parent {
  type: 'root'
}
```
### `Element`

Elements are syntactic components that exist at the same or greater scope than a paragraph.

```idl
interface Element <: Parent {
  children: [ElementContent]
}
```

### `GreaterElement`
A container can contain directly any other [Element] or [GreaterElement]

```idl
interface GreaterElement <: Parent {
  children: [GreaterElementContent]
}
```
### `Lesser Element`

```idl
interface LesserElement <: Element {
  value: string?
  children: [Object?]
}
```

Lesser elements ([Element](#element)) are elements that cannot contain any other elements. some of them contain objects.

A `value` field can be present.
It represents the CONTENTS of the element.

A `children` must be present.
It represents the children of the element. If `value` field is present, a `children` must be an empty list.

### `Object`

```idl
interface Object <: Node {
  value: any
}
```

Objects are syntactic components that exist with a smaller scope than a paragraph, and so can be contained within a paragraph.

### `Heading`
### `Section`
### `Center Block`

```idl
interface CenterBlock <: GreaterElement {
  type: 'centerBlock'
  children: [GreaterElementContent]
}
```

**Center Block** represents a block of text that is centered.

for example, the following org:

```org
#+BEGIN_CENTER
first line
second line
#+END_CENTER
```

Yields:

```json
{
  "type": "centerBlock",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "first line"
        }
      ]
    },
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "second line"
        }
      ]
    }
  ]
}
```
### `Drawer`
### `Property Drawer`
### `Dynamic Block`
### `Footnote Definitions`
### `Inline Task`
### `List Item`
### `Plain List`
### `Table`
### `Comment Block`

```idl
interface CommentBlock <: LesserElement {
  type: 'commentBlock'
  children: [Paragraph]
}
```

**Comment Block** represents a block of text that is a comment. It can contain markup.

for example, the following org:

```org
#+BEGIN_COMMENT
first line
second line
#+END_COMMENT
```

Yields:

```json
{
  "type": "commentBlock",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "first line"
        }
      ]
    },
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "second line"
        }
      ]
    }
  ]
}
```
### `Example Block`

```idl
interface ExampleBlock <: LesserElement {
  type: 'exampleBlock'
  value: string?
}
```
*Example Block* represents a block of text that is an example. It is not subject to markup.

for example, the following org:

```org
#+BEGIN_EXAMPLE
first line
second *line*
#+END_EXAMPLE
```

Yields:

```json
{
  "type": "exampleBlock",
  "value": "first line\nsecond *line*"
}
```

### `Export Block`

```idl
interface ExportBlock <: LesserElement {
  type: 'exportBlock'
  backend: string
  value: string
}
```

**Export Block** represents a block of text that is exported to a backend.

A `backend` field must be present.
It represents the backend that the block is exported to.

for example, the following org:

```org
#+BEGIN_EXPORT html
<html></html>
#+END_EXPORT
```

Yields:

```json
{
  "type": "exportBlock",
  "backend": "html",
  "value": "<html></html>"
}
```
### `Source Block`

```idl
interface SourceBlock <: GreaterElement {
  type: 'sourceBlock'
  language: string
  arguments: string
  contents: string?
}
```

**Source Block* represents a block of text that is source code.

A `language` field must be present.
It represents the language of computer code being marked up.

If `language` field is present, a `arguments` field can be present. It represents the arguments of the interpreter.

A `contents` field can be present.
It represents the contents of the source code.

for example, the following org:

```org
#+BEGIN_SRC js -n2 :var x=1
console.log('hello world');
#+EMD_SRC
```

Yields:

```json
{
  "type": "sourceBlock",
  "language": "js",
  "switches": "-n2",
  "arguments": ":var x=1",
  "contents": "console.log('hello world');"
}
```
### `Verse Block`

```idl
interface VerseBlock <: LesserElement {
  type: 'verseBlock'
  value: string?
}
```

**Verse Block** represents a block of text that is a verse. It is not subject to markup and reserve indents.

for example, the following org:

```org
#+BEGIN_VERSE
   first line
second line
#+END_VERSE
```

Yields:

```json
{
  "type": "verseBlock",
  "value": "   first line\nsecond line"
}
```

### `Clock`
### `Diary Sexp`
### `Planning`
### `Comment`
### `Fixed Width`
### `Horizontal Rule`
### `Keyword`
### `Babel Call`
### `Affiliated Keyword`
### `LaTeX Environment`
### `Node Property`
### `Table Row`
### `Entity`
### `LaTeX Fragment`
### `Export Snippet`
### `Footnote Reference`
### `Citation`
### `Citation Reference`
### `Inline BabelCall`
### `Inline SrcBlock`
### `Line Break`
### `Link`

```idl
interface Link <: Object {
  type: 'link'
  subType: 'regular' | 'radio' | 'angle' | 'plain'
  rawLink: string
  description: string
}
```

Links are syntactic components that exist with a smaller scope than a paragraph, and so can be contained within a paragraph.

`subType` must be present.
It represents the subtype of the link. There are four subtypes, which are: `regular`, `radio`, `angle`, and `plain`.

`rawLink` must be present.
It represents the raw link string, which is the link string before any processing.

`description` can be present.
It represents the description of the link.

#### `Radio Link`

#### `Plain link`

```idl
interface PlainLink <: Link {
  type: 'link'
  subType: 'plain'
}

PlainLink includes Resource
```

**Plain Link** includes **Resource** ([Resource](#resource)).

for example, the following orgmode:

```org
https://orgmode.org.
```

Yields:

```js
{
  type: 'link',
  subType: 'plain',
  rawLink: 'https://orgmode.org',
  resourceType: 'protocol',
  protocol: 'https',
  path: 'orgmode.org'
}
```

#### `Angle Link`

```idl
interface AngleLink <: Link {
  type: 'link'
  subType: 'angle'
}

AngleLink includes Resource
```

Angle-type essentially provide a method to disambiguate plain links from surrounding text.

for example, the following orgmode:

```org
<https:example.com>
```

Yields:

```js
{
  type: 'link',
  subType: 'angle',
  rawLink: 'https:example.com',
  resourceType: 'protocol',
  protocol: 'https',
  path: 'example.com'
}
```

#### `Regular Link`

```idl
interface RegularLink <: Link {
  subType: 'regular'
}

RegularLink includes Resource
```

**Regular links** are the most common type of link, and structured according to one of the following two patterns:
[[PATHREG]] or [[PATHREG][DESCRIPTION]].

**Regular links** includes [Resource](#resource).

for example, the following orgmode:

```org
[[https://example.com][Example]]
```

Yields:

```js
{
  type: 'link',
  subType: 'regular',
  rawLink: 'https://example.com',
  type: 'protocol',
  protocol: 'https',
  path: 'example.com',
  description: 'Example'
}
```
and the following orgmode:

```org
[[a.png]]
```

Yields:

```js
{
  type: 'link',
  subType: 'regular',
  rawLink: 'a.png',
  resourceType: 'file',
  filename: 'a.png'
}
```

and the following orgmode:

```org
[[id:1234]]
```

Yields:

```js
{
  type: 'link',
  subType: 'regular',
  rawLink: 'id:1234',
  resourceType: 'id',
  id: '1234'
}
```

### Macro
### Target
### Statistic Cookie
### Subscript
### Superscript
### Table Cell
### Timestamp
### `Paragraph`

```idl
interface Paragraph <: Parent {
  type: 'paragraph'
  children: [Object]
}
```

Paragraphs are the default element, which means that any unrecognized context is a paragraph.

Paragraphs can contain the standard set of objects.

For example, the following orgmode:

```org
Alpha bravo charlie.
```

yields:

```js
{
  type: 'paragraph',
  children: [
    {type: 'text', value: 'Alpha bravo charlie.'}
  ]
}
```
### `Text`

Any string that doesn’t match any other object can be considered a plain text object.

```idl
interface Text <: Literal {
  type: 'text'
}
```

For example, the following orgmode:

```org
Alpha bravo charlie.
```

Yields:

```js
{type: 'text', value: 'Alpha bravo charlie.'}
```

### `Text Markup`

```
interface TextMarkup <: Literal {
  type: 'bold' | 'italic' | 'underline' | 'verbatim' | 'code' | 'strike-through'
}
```

[Text](#Text) can be emphasized. There are six text markup objects to emphasis a text as follows:

- *, a bold object,
- /, an italic object,
- _ an underline object,
- =, a verbatim object,
- ~, a code object
- +, a strike-through object.

For example, the following markdown:

```org
*alpha*, /italic/
```

Yields:

```js
{
  type: 'paragraph',
  children: [
    {
      type: 'bold', value: 'alpha'
    },
    {
      type:'text',
      value:','}
    {
      type: 'italic', value: 'bravo'
    }
  ]
}
```

Text markup can not contain another text markup.

## `Mixin`

### `Resource`

```idl
interface mixin Resource {
  pathType: 'file' | 'id' | 'custom-id' | 'fuzzy' | 'protocol'
}
```

*Resource* represents a reference to resource.

A `pathType` field must be present.
It represents the type of the path. There are five types, which are: `file`, `id`, `custom-id`, `fuzzy`, and `protocol`.

#### File

```idl
interface File <: Resource {
  resourceType: 'file'
  filename: string
}
```

A `filename` field must be present.
It represents an absolute or relative file path.

#### Protocol

```idl
interface Protocol <: Resource {
  resourceType: 'protocol'
  protocol: string
  path: string
}
```

A `protocol` field must be present.
It represents the protocol of the link. The default value is one of `shell`, `news`, `mailto`, `https`, `http`, `ftp`, `help`, `file`, and `elisp`.

A `path` field must be present.
It represents the inner part of the path.

#### ID

```idl
interface Id <: Resource {
  resourceType: 'id'
  id: string
}
```

A `id` field must be present.
It represents the id of the link.

#### Custom ID

```idl
interface CustomId <: Resource {
  resourceType: 'custom-id'
  customId: string
}
```

A `customId` field must be present.
It represents the custom id of the link.

#### Code Ref

```idl
interface CodeRef <: Resource {
  resourceType: 'coderef'
  codeRef: string
}
```

A `codeRef` field must be present.
It represents the code ref of the link.

#### Fuzzy

```idl
interface Fuzzy <: Resource {
  resourceType: 'fuzzy'
  fuzzy: string
}
```

A `fuzzy` field must be present.
It represents the fuzzy path of the link.

## Glossary

See the [unist glossary][glossary].

## References

*   **unist**:
    [Universal Syntax Tree][unist].
    T. Wormer; et al.
*   **orgmode**:
    [orgmode][].
    C Dominik; et al.
*   **Web IDL**:
    [Web IDL][webidl],
    C. McCormack.
    W3C.

## Related

*   [mdast](https://github.com/syntax-tree/mdast)
    — Markdown Abstract Syntax Tree format
*   [hast](https://github.com/syntax-tree/hast)
    — Hypertext Abstract Syntax Tree format
*   [nlcst](https://github.com/syntax-tree/nlcst)
    — Natural Language Concrete Syntax Tree format
*   [xast](https://github.com/syntax-tree/xast)
    — Extensible Abstract Syntax Tree

## Contribute

See [`contributing.md`][contributing] in [`syntax-tree/.github`][health] for
ways to get started.
See [`support.md`][support] for ways to get help.
Ideas for new utilities and tools can be posted in [`syntax-tree/ideas`][ideas].

A curated list of awesome syntax-tree, unist, mdast, hast, xast, and nlcst
resources can be found in [awesome syntax-tree][awesome].

This project has a [code of conduct][coc].
By interacting with this repository, organization, or community you agree to
abide by its terms.

## License

[CC-BY-4.0][license] © [Hsin-Yi Chen][author]

<!-- Definitions -->

[health]: https://github.com/syntax-tree/.github

[contributing]: https://github.com/syntax-tree/.github/blob/HEAD/contributing.md

[support]: https://github.com/syntax-tree/.github/blob/HEAD/support.md

[coc]: https://github.com/syntax-tree/.github/blob/HEAD/code-of-conduct.md

[awesome]: https://github.com/syntax-tree/awesome-syntax-tree

[ideas]: https://github.com/syntax-tree/ideas

[license]: https://creativecommons.org/licenses/by/4.0/

[author]: https://cuilian.io

[releases]: https://github.com/cuilianhq/omast/releases

[latest]: https://github.com/cuilianhq/omast/releases/tag/4.0.0

[dfn-node]: https://github.com/syntax-tree/unist#node

[dfn-unist-parent]: https://github.com/syntax-tree/unist#parent

[dfn-unist-literal]: https://github.com/syntax-tree/unist#literal

[term-tree]: https://github.com/syntax-tree/unist#tree

[term-child]: https://github.com/syntax-tree/unist#child

[term-sibling]: https://github.com/syntax-tree/unist#sibling

[term-root]: https://github.com/syntax-tree/unist#root

[term-head]: https://github.com/syntax-tree/unist#head

[unist]: https://github.com/syntax-tree/unist

[syntax-tree]: https://github.com/syntax-tree/unist#syntax-tree

[javascript]: https://www.ecma-international.org/ecma-262/9.0/index.html

[Dart]: https://dart.dev/

[webidl]: https://heycam.github.io/webidl/

[orgmode]: https://orgmode.org

[glossary]: https://github.com/syntax-tree/unist#glossary

[unified]: https://github.com/unifiedjs/unified

[hast]: https://github.com/syntax-tree/hast

[dfn-node]: https://github.com/syntax-tree/unist#node

[dfn-unist-parent]: https://github.com/syntax-tree/unist#parent

[dfn-unist-literal]: https://github.com/syntax-tree/unist#literal
