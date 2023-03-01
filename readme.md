# omast

**O**rg**M**ode **A**bstract **S**yntax **T**ree.

***

**omast** is a specification for representing orgmode in a [syntax
tree][syntax-tree].
It implements **[unist][]**.

This document may not be released.
See [releases][] for released documents.

## Contents

*   [Introduction](#introduction)
    *   [Where this specification fits](#where-this-specification-fits)
*   [Types](#types)
*   [Nodes](#nodes)
*   [Glossary](#glossary)
*   [References](#references)
*   [Related](#related)
*   [Contribute](#contribute)
*   [Acknowledgments](#acknowledgments)
*   [License](#license)

## Introduction

This document defines a format for representing [orgmode][] as an [abstract
syntax tree][syntax-tree].

This specification is written in a [Web IDL][webidl]-like grammar.

### Where this specification fits

omast extends [unist][], a format for syntax trees, to benefit from its ecosystem of utilities.

omast is not limited to Dart and can be used in other programming languages.

## Types

If you are using Dart, you can use the omast types by installing them
with pub.dev:

```sh
dart pub add omast
```

```sh
flutter pub add omast
```

## Nodes

### `Parent`

```idl
interface Parent <: UnistParent {
  children: [Element | Object]
}
```

**Parent** ([UnistParent][dfn-unist-parent]) represents an abstract interface in mdast containing other nodes (said to be children).
Its content is limited to only either Element or Object.

### `Literal`

```
interface Literal <: UnistLiteral {
  value: string
}
```

**Literal** ([**UnistLiteral**][dfn-unist-literal]) represents an abstract interface in mdast containing a value.

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

Lesser elements are elements that cannot contain any other elements.

```idl
interface LesserElement <: Parent {
  children: [GreaterElementContent]
}
```

### `Object`

```idl
interface Object <: Node {
  value: any
}
```

Objects are syntactic components that exist with a smaller scope than a paragraph, and so can be contained within a paragraph.

### Heading
### Section
### Center Block
### Quote Block
### Drawer
### Property Drawer
### Dynamic Block
### Footnote Definitions
### Inline Task
### List-Item
### Plain List
### Table
### Comment Block
### Example Block
### Export Block
### Soruce Block
### Verse Block
### Clock
### Diary Sexp
### Planning
### Comment
### Fixed Width
### Horizontal Rule
### Keyword
### Babel Call
### Affiliated Keyword
### LaTeX Environment
### Node Propertie
### Table Row
### Entity
### LaTeX Fragment
### Export Snippet
### Footnote Reference
### Citation
### Citation Reference
### Inline BabelCall
### Inline SrcBlock
### Line Break
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

```interface PlainLink <: Link {
  type: 'link'
  subType: 'plain'
  pathplain: string
}
```

for example:

```org
https://orgmode.org.
```

yield:

```js
{
  type: 'link',
  subType: 'plain',
  rawLink: 'https://orgmode.org',
  protocol: 'https',
  pathplain: 'orgmode.org'
}
```

#### `Angle Link`

#### `Regular Link`

```idl
interface RegularLink <: Link {
  type: 'link'
  pathreg: PathReg
}
```

**Regular links** are the most common type of link, and structured according to one of the following two patterns:
[[PATHREG]] or [[PATHREG][DESCRIPTION]].


PATHREG is a union type of all seven following
annotated patterns

```idl
interface ProtocolPath PathReg {
  type: 'protocol'
  protocol: string
  pathinner: string
}

interface FilePath <: PathReg {
  type: 'file'
  filename: string
}

interface IdPath <: PathReg {
  type: 'id'
  id: string
}

interface CustomId <: PathReg {
  type: 'custom-id'
  customId: string
}

interface CodeRef <: PathReg {
  type: 'coderef'
  codeRef: string
}

interface Fuzzy <: PathReg {
  type: 'fuzzy'
  fuzzy: string
}
```

for example:

```org
[[https://example.com][Example]]
```

yield:

```js
{
  type: 'link',
  subType: 'regular',
  rawLink: 'https://example.com',
  pathreg: {
    type: 'protocol',
    protocol: 'https',
    pathinner: 'example.com',
  }
  description: 'Example'
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

### Text Markup`

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
