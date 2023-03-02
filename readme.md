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
    - [`Planning`](#planning)
    - [`Timestamp`](#timestamp)
    - [`Clock`](#clock)
    - [`Diary Sexp`](#diary-sexp)
    - [`Section`](#section)
    - [`Drawer`](#drawer)
    - [`Property Drawer`](#property-drawer)
    - [`Node Property`](#node-property)
    - [`Paragraph`](#paragraph)
    - [`Line Break`](#line-break)
    - [`Horizontal Rule`](#horizontal-rule)
    - [`Text`](#text)
    - [`Text Markup`](#text-markup)
    - [`Subscript`](#subscript)
    - [`Superscript`](#superscript)
    - [`LaTeX Environment`](#latex-environment)
    - [`LaTeX Fragment`](#latex-fragment)
    - [`Entity`](#entity)
    - [`Inline Task`](#inline-task)
    - [`Plain List`](#plain-list)
    - [`List Item`](#list-item)
    - [`Table`](#table)
    - [`Table Row`](#table-row)
    - [`Table Cell`](#table-cell)
    - [`Center Block`](#center-block)
    - [`Dynamic Block`](#dynamic-block)
    - [`Comment Block`](#comment-block)
    - [`Example Block`](#example-block)
    - [`Export Block`](#export-block)
    - [`Source Block`](#source-block)
    - [`Verse Block`](#verse-block)
    - [`Comment`](#comment)
    - [`Fixed Width`](#fixed-width)
    - [`Link`](#link)
      - [`Radio Link`](#radio-link)
      - [`Plain link`](#plain-link)
      - [`Angle Link`](#angle-link)
      - [`Regular Link`](#regular-link)
    - [`Target`](#target)
    - [`Radio Target`](#radio-target)
    - [`Footnote Reference`](#footnote-reference)
    - [`Footnote Definitions`](#footnote-definitions)
    - [`Citation`](#citation)
    - [`Citation Reference`](#citation-reference)
    - [`Macro`](#macro)
    - [`Export Snippet`](#export-snippet)
    - [`Babel Call`](#babel-call)
    - [`Inline BabelCall`](#inline-babelcall)
    - [`Inline SrcBlock`](#inline-srcblock)
    - [`Statistic Cookie`](#statistic-cookie)
    - [`Keyword`](#keyword)
    - [`Affiliated Keyword`](#affiliated-keyword)
  - [`Mixin`](#mixin)
    - [`Resource`](#resource)
      - [File](#file)
      - [Protocol](#protocol)
      - [ID](#id)
      - [Custom ID](#custom-id)
      - [Code Ref](#code-ref)
      - [Fuzzy](#fuzzy)
    - [`Affiliated Keywords`](#affiliated-keywords)
    - [Planable](#planable)
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

```idl
interface Heading <: Parent {
  type: 'heading'
  depth: number
  title: Paragraph | Link
  commented: bool
  todoKeyword?: string
  priority?: string
  tags?: [string]
  children: [Section?]
}

Heading includes Planable
```

**Heading** ([Parent](#parent)) represents a heading of a section.

**Heading** includes the mixin [Planable](#planable).

A `depth` field must be present.
It represents the depth of the heading. the value of `depth` must greater than 0.

A `commented` field must be present.
It represents whether the heading is commented or not.

A `title` field must be present.
It represents the title of the heading.

A `todoKeyword` field can be present.
It represents the *todo keyword* of the heading.

A `priority` field can be present.
It represents the priority of the heading.

A `tags` field can be present.
It represents the tags of the heading.

A `children` field must be present.
It represents the sections the heading contained. A heading may have no section.

for  example, the following content:

```org
* TODO [#A] Heading :tag1:tag2:
This is a paragraph
```

Yields:

```json
{
  "type": "heading",
  "depth": 1,
  "commented": false,
  "todoKeyword": "TODO",
  "priority": "A",
  "tags": ["tag1", "tag2"],
  "title": {
    "type":"paragraph",
    "children": {
      "type":"text",
      "value":"Heading"
    },
  "children": [{
    "type": "section",
    "children": [{
      "type": "paragraph",
      "children": [{
        "type": "text",
        "value": "This is a paragraph"
      }]
    }]
  }]
}
```

### `Planning`

```idl
interface Planning <: Node {
  type: 'planning'
  scheduled: Timestamp?
  deadline: Timestamp?
  closed: Timestamp?
}
```

**Planning** ([Node](#dfn-node)) represents a planning.

A `scheduled` field can be present.
It represents the scheduled time of the planning.

A `deadline` field can be present.
It represents the deadline of the planning.

A `closed` field can be present.
It represents the closed time of the planning.

for example, the following org:

```org
    SCHEDULED: <1999-03-31 Wed>
```

Yields:

```json
{
  "type": "planning",
  "scheduled": {
    "type": "timestamp",
    "start": "Wed Mar 31 1999 00:00:00"
  }
}
```
### `Timestamp`

```idl
interface Timestamp <: Object {
  type: 'timestamp'
  subType: 'diary' | active' | 'inactive' | 'active-range' | 'inactive-range'
  start: Date
  end: Date?
  repeater: String?
  warning: String?
}
```

**Timestamp** ([Object](#object)) represents a timestamp.

A `subType` field must be present.
It represents the seven subtype of the timestamp as followings:

```
<%%(SEXP)>                                                     (diary)
<DATE TIME REPEATER-OR-DELAY>                                  (active)
[DATE TIME REPEATER-OR-DELAY]                                  (inactive)
<DATE TIME REPEATER-OR-DELAY>--<DATE TIME REPEATER-OR-DELAY>   (active range)
<DATE TIME-TIME REPEATER-OR-DELAY>                             (active range)
[DATE TIME REPEATER-OR-DELAY]--[DATE TIME REPEATER-OR-DELAY]   (inactive range)
[DATE TIME-TIME REPEATER-OR-DELAY]                             (inactive range)
```

A `start` field must be present.
It represents the start date of the timestamp.

A `end` field can be present.
It represents the end date of the timestamp.

A `repeater` field can be present.
It represents the repeater of the timestamp.

A `warning` field can be present.
It represents the warning delay of the timestamp.

for example, the following content:

```org
<1997-11-03 Mon 19:15 +1m -3d>
```

Yields:

```json
{
  "type": "timestamp",
  "subType": "active",
  "start": "1997-11-03T19:15:00.000Z",
  "repeater": "+1m",
  "warning": "-3d"
}
```
### `Clock`
### `Diary Sexp`
### `Section`

```idl
interface Section <: Element {
  type: 'section'
  children: [ElementContent]
}
```

**Sections** ([Element](#element)) contain one or more non-heading elements.

for example, the following content:

```org
An introduction.
* A Heading
Some text.
```

Yields:

```json
{
  "type": "section",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "An introduction."
        }
      ]
    },
    {
      "type": "heading",
      "depth": 1,
      "commented": false,
      "title": {
        "type": "paragraph",
        "children": [
          {
            "type": "text",
            "value": "A Heading"
          }
        ]
      },
      "children": [
        {
          "type": "paragraph",
          "children": [
            {
              "type": "text",
              "value": "Some text."
            }
          ]
        }
      ]
  ]
}
```

### `Drawer`

```idl
interface Drawer <: GreaterElement {
  type: 'drawer'
  name: string
  children: [GreaterElementContent]
}
```

**Drawer** represents a drawer that contains arbitrary content associated with an entry.

A `name` field must be present.
It represents the name of the drawer.

A `children` field can not contain any other [Drawer](#drawer) and [Heading](#heading).

for example, the following org:

```org
:DRAWERNAME:
This is inside the drawer.
:END:
```

Yields:

```json
{
  "type": "drawer",
  "name": "DRAWERNAME",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "This is inside the drawer."
        }
      ]
    }
  ]
}
```

### `Property Drawer`

```idl
interface PropertyDrawer <: Drawer {
  type: 'property-drawer'
  children: [NodeProperty?]
}
```

**Property Drawer** ([Drawer](#drawer)) represents a drawer that contains properties attached to a [Heading](#heading) or [Inline Task](#inline-task) or a special [Section](#section) called zero section.

A `children` field must be present and can only contain [Node Property](#node-property).

for example, the following content:

```org
:PROPERTIES:
:ID:       0a1b2c3d
:YEAR:      2013
```

Yields:

```json
{
  "type": "property-drawer",
  "name": "PROPERTIES",
  "children": [
    {
      "type": "node-property",
      "name": "ID",
      "value": "0a1b2c3d"
    },
    {
      "type": "node-property",
      "name": "YEAR",
      "value": "2013"
    }
  ]
}
```

### `Node Property`

```idl
interface NodeProperty <: Object {
  type: 'node-property'
  name: string
  value?: string
}
```

**Node Property** ([Lesser Element](#lesser-element)) represents a node property and can only exists in [Property Drawer](#property-drawer).

A `name` field must be present.
It represents the name of the node property.

A `value` field can be present.
It represents the value of the node property.

for example, the following org:

```org
:LOCATION: Beijing
```

Yields:

```json
{
  "type": "node-property",
  "name": "LOCATION",
  "value": "Beijing"
}
```

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
### `Line Break`

```idl
interface LineBreak <: Node {
  type: 'line-break'
}
```

**Line Break** ([Node][dfn-node]) represents a line break, such as in [Quote Block](#quote-block) and [Paragraph](#paragraph).

for example, the following content:

```org
#+BEGIN_QUOTE
a1 \\
a2
#+END_QUOTE
```

Yields:

```json
{
  "type": "quote-block",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "a1"
        },
        {
          "type": "line-break"
        },
        {
          "type": "text",
          "value": "a2"
        }
      ]
    }
  ]
}
```
### `Horizontal Rule`

```idl
interface HorizontalRule <: Node {
  type: 'horizontal-rule'
}

HorizontalRule includes AffiliatedKeywords
```

**Horizontal Rule** ([Node](#dfn-node)) represents a horizontal rule.

for example, the following content:

```org
-----
```

Yields:

```json
{
  "type": "horizontal-rule"
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
### `Subscript`

```idl
interface Subscript <: Object {
  type: 'subscript'
  value: string
}
```

**Subscript** ([Object](#object)) represents a subscript.

for example, the following content, x<sub>y</sub>:

```org
x_y
```

Yields:

```json
{
  "type": "paragraph",
  "children": [
    {
      "type": "text",
      "value": "x"
    },
    {
      "type": "subscript",
      "value": "y"
    }
  ]
}
```
### `Superscript`

```idl
interface Superscript <: Object {
  type: 'superscript'
  value: string
}
```

**Superscript** ([Object](#object)) represents a superscript.

for example, the following content, x<sup>y</sup>:

```org
x^y
```

Yields:

```json
{
  "type": "paragraph",
  "children": [
    {
      "type": "text",
      "value": "x"
    },
    {
      "type": "superscript",
      "value": "y"
    }
  ]
}
```
### `LaTeX Environment`

```idl
interface LaTeXEnvironment <: Node {
  type: 'latex-environment'
  name: string
  value: string
}

LaTeXEnvironment includes AffiliatedKeywords
```

**LaTeX Environment** ([Node][dfn-node]) represents a LaTeX environment.

**LaTeX Environment** includes the mixin [Affiliated Keywords](#affiliated-keywords).

A `name` field must be present.
It represents the name of the LaTeX environment.

A `value` field must be present.
It represents the contents of the LaTeX environment.

for example, the following org:

```org
\begin{align*}
2x - 5y &= 8 \\
3x + 9y &= -12
\end{align*}
```

Yields:

```json
{
  "type": "latex-environment",
  "name": "align*",
  "value": "2x - 5y &= 8 \\\\\n3x + 9y &= -12"
}
```
### `LaTeX Fragment`

```idl
interface LaTeXFragment <: Node {
  type: 'latex-fragment'
  name?: string
  value: string
}
```

**LaTeX Fragment** ([Node](#dfn-node)) represents a LaTeX fragment.

A `name` field can be present.
It represents the name of the LaTeX fragment.

A `value` field must be present.
It represents the contents of the LaTeX fragment.

for example, the following content:

```org
\enlargethispage{2\baselineskip}
\(e^{i \pi}\)
```

Yields:

```json
{
  "type": "latex-fragment",
  "name": "enlargethispage",
  "value": "2\\baselineskip"
},
{
  "type": "latex-fragment",
  "name": "display",
  "value": "e^{i \\pi}"
}
```

and the following content:

```org
$$1+1=2$$
```

Yields:

```json
{
  "type": "latex-fragment",
  "value": "1+1=2"
}
```
### `Entity`

```idl
interface Entity <: Node {
  type: 'entity'
  value: string
}
```
**Entity** ([Node][dfn-node]) represents an entity described in [Org Entities](https://orgmode.org/worg/org-syntax.html#Entities_List). An entity is an special character that can be translated to a unicode character.

A `value` field must be present.
It represents the value of the entity

for example, the following content:

```org
\zeta
```

Yields:

```json
{
  "type": "entity",
  "value": "ζ"
}
```
### `Inline Task`
### `Plain List`

```idl
interface PlainList <: Node {
  type: 'plain-list'
  subType: 'unordered' | 'ordered' | 'descriptive'
  children: [PlainList | ListItem]
}

PlainList includes AffiliatedKeywords
```

**Plain List** ([Parent](#parent)) represents a list of items.

**Plain List** includes the mixin [Affiliated Keywords](#affiliated-keywords).

**Plain List** can contain [Plain List](#plain-list) and [List Item](#list-item).

A `subType` field must be present.
It represents the the list is either an ordered plain list or an unordered plain list or a descriptive list.

for example, the following content:

```org
1. item 1
2. [X] item 2
   - some tag :: item 2.1
```

Yields:

```json
{
  "type": "plain-list",
  "subType": "ordered",
  "children": [
    {
      "type": "list-item",
      "children": [
        {"type":"paragraph","children":[{"type":"text","value":"item 1"}]}
      ]
    },
    {
      "type": "list-item",
      "children": [
        {
          "type":"paragraph",
          "children":[
            {"type":"text","value":"item 2"}
          ]},
        {
          "type": "plain-list",
           "subType": "descriptive",
          "children": [
           {
             "type": "list-item",
             "tag": "some tag",
             "children": [
               {
                  "type":"paragraph",
                  "children":[
                    {"type":"text","value":"item 2.1"}
                   ]
                }
             ]
           }
          ]
        }
      ],
    }
  ]
}
```
### `List Item`

```idl
interface ListItem <: Node {
  type: 'list-item'
  bullet: string
  counterSet?: string
  checkedBox?: 'on' | 'off' | 'trans'
  tag?: [string]
  children: [ListItemContent]
}
```

A `bullet` field must be present.
It represents the bullet of the item. the value is either asterisk (*), hyphen (-), or plus sign (+) or a number or a single letter (a-z)..

A `counterSet` field can be present.
It represents the counter set of the item.

A `checkedBox` field can be present.
It represents the state of the checkbox of the item.

A `tag` field can be present.
It represents the tag of the item.

For an example of a list item, see [Plain List](#plain-list).
### `Table`
### `Table Row`
### `Table Cell`
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
### `Dynamic Block`
### `Comment Block`

```idl
interface CommentBlock <: Parent {
  type: 'commentBlock'
  children: [Paragraph]
}

CommentBlock includes AffiliatedKeywords
```

**Comment Block** ([Parent](#parent)) represents a block of text that is a comment. It can contain markup.

**Comment Block** includes the mixin [Affiliated Keywords](#affiliated-keywords).

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
  "type": "comment-block",
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

ExampleBlock includes AffiliatedKeywords
```

*Example Block* represents a block of text that is an example. It is not subject to markup.

**Example Block** includes the mixin [Affiliated Keywords](#affiliated-keywords).

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
interface ExportBlock <: Node {
  type: 'export-block'
  backend: string
  value: string?
}

ExportBlock includes AffiliatedKeywords
```

**Export Block** ([Node][dfn-node])) represents a block of text that is exported to a backend.

**Export Block** includes the mixin [Affiliated Keywords](#affiliated-keywords).

A `backend` field must be present.
It represents the backend that the block is exported to.

A `value` field must be present.
It represents the contents of the block.

for example, the following org:

```org
#+BEGIN_EXPORT html
<html></html>
#+END_EXPORT
```

Yields:

```json
{
  "type": "export-block",
  "backend": "html",
  "value": "<html></html>"
}
```
### `Source Block`

```idl
interface SourceBlock <: Node {
  type: 'source-block'
  language: string
  arguments: string
  value: string?
}

SourceBlock includes AffiliatedKeywords
```

**Source Block* ([Node][dfn-node]) represents a block of text that is source code.

**Source Block** includes the mixin [Affiliated Keywords](#affiliated-keywords).

A `language` field must be present.
It represents the language of computer code being marked up.

If `language` field is present, a `arguments` field can be present. It represents the arguments of the interpreter.

A `value` field can be present.
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
  "type": "source-block",
  "language": "js",
  "switches": "-n2",
  "arguments": ":var x=1",
  "value": "console.log('hello world');"
}
```
### `Verse Block`

```idl
interface VerseBlock <: Node {
  type: 'verse-block'
  value: string?
}

VerseBlock includes AffiliatedKeywords
```

**Verse Block** ([Node][dfn-node]) represents a block of text that is a verse. It is not subject to markup and reserve indents.

**Verse Block** includes the mixin [Affiliated Keywords](#affiliated-keywords).

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
  "type": "verse-block",
  "value": "   first line\nsecond line"
}
```

### `Comment`
### `Fixed Width`

```idl
interface FixedWidth <: Node {
  type: 'fixed-width'
  value: string
}

FixedWidth includes AffiliatedKeywords
```

**Fixed Width** ([Node](#dfn-node)) represents a fixed width area.

**Fixed Width** includes the mixin [Affiliated Keywords](#affiliated-keywords).

A `value` field must be present.

for example, the following org:

```org
: This is a
: fixed width area
```

Yields:

```json
{
  "type": "fixed-width",
  "value": "This is a\nfixed width area"
}
```

### `Link`

```idl
interface Link <: Object {
  type: 'link'
  subType: 'regular' | 'radio' | 'angle' | 'plain'
  rawLink: string
  description?: string
}

Link includes AffiliatedKeyword
```

**Link** represents a hyperlink.

**Link** includes mixins **Affiliated Keyword** ([Affiliated Keyword](#affiliated-keyword)).

A `subType` field must be present.
It represents the subtype of the link. There are four subtypes, which are: `regular`, `radio`, `angle`, and `plain`.

A `rawLink` field must be present.
It represents the raw link string, which is the link string before any processing.

A `description` field can be present.
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

**Plain Link** ([Link](#link)) represents plain-type links are links that are not surrounded by angle brackets.

**Plain Link** includes mixins **Resource** ([Resource](#resource)).

for example, the following content:

```org
https://orgmode.org.
```

Yields:

```json
{
  "type": "link",
  "subType": "plain",
  "rawLink": "https://orgmode.org",
  "resourceType": "protocol",
  "protocol": "https",
  "path": "orgmode.org"
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

**Angle Link** ([Link](#link)) represents angle-type links are links that are surrounded by angle brackets.

**Angle Link** includes mixins **Resource** ([Resource](#resource)).

for example, the following content:

```org
<https:example.com>
```

Yields:

```json
{
  "type": "link",
  "subType": "angle",
  "rawLink": "https:example.com",
  "resourceType": "protocol",
  "protocol": "https",
  "path": "example.com"
}
```
#### `Regular Link`

```idl
interface RegularLink <: Link {
  subType: 'regular'
}

RegularLink includes Resource
```

**Regular links**([Link](#link)) represents the most common type of link, and structured according to one of the following two patterns:
[[PATHREG]] or [[PATHREG][DESCRIPTION]].

**Regular links** includes [Resource](#resource).

for example, the following content:

```org
#+ATTR_HTML: :title The Org mode website :style color:red;
[[https://example.com][Example]]
```

Yields:

```json
{
  "type": "link",
  "subType": "regular",
  "rawLink": "https://example.com",
  "resourceType": "protocol",
  "protocol": "https",
  "path": "example.com",
  "description": "Example"
  "affiliated": [
    {
      "type": "affiliated-keyword",
      "backend": "html",
      "value": ":title The Org mode website :style color:red;"
    }
  ]
}
```

and the following content:

```org
[[a.png]]
```

Yields:

```json
{
  "type": "link",
  "subType": "regular",
  "rawLink": "a.png",
  "resourceType": "file",
  "path": "a.png"
}
```

and the following content:

```org
[[id:1234]]
```

Yields:

```json
{
  "type": "link",
  "subType": "regular",
  "rawLink": "id:1234",
  "resourceType": "id",
  "path": "1234"
}
```
### `Target`

``` idl
interface Target <: Node {
  type: 'target'
  value: Text
}
```

**Target** ([Node][dfn-node]) represents a target that a [**Regular Link**](#regular-link) links to. It is usually used to link to a specific location in the document as an internal link.

A `value` field must be present.
It represents the value of the target

for example, the following content:

```org
<<important>>
```

Yields:

```json
{
  "type": "target",
  "value": {
    "type": "text",
    "value": "important"
  }
}
```
### `Radio Target`

```idl
Interface RadioTarget <: Target {
  type: 'radio-target'
  value: MinimalSetObject
}
```

**Radio Target** ([Node][dfn-node]) represents a radio target that a [**Radio Link**](#radio-link) links to.

A `value` field must be present.
It represents the value of the target.

for example, the following content:

```org
<<<*important* information>>>
```

Yields:

```json
{
  "type": "radio-target",
  "value": {
      "type": "paragraph",
      "children": [
        {
          "type": "bold",
          "value": "important"
        },
        {
          "type": "text",
          "value": "information"
        }
      ]
  }
}
```
### `Footnote Reference`
### `Footnote Definitions`
### `Citation`
### `Citation Reference`
### `Macro`
### `Export Snippet`
### `Babel Call`
### `Inline BabelCall`
### `Inline SrcBlock`
### `Statistic Cookie`

```idl
interface StatisticCookie <: Object {
  type: 'statistic-cookie'
  percentage?: number
  current?: number
  total?: number
}
```

**Statistic Cookie** ([Object](#object)) represents a statistic cookie in a [Heading](#heading) that usually used to counts any TODO entries or checkbox in the heading's children.

A `percentage` field can be present.
It represents the percentage. It is a number between 0 and 100.

A `current` field can be present.
It represents the current number. It is a number greater than 0.

A `total` field can be present.
It represents the total number. It is a number greater than 0.

for example, the following content:

```org
[40%]
```

Yields:

```json
{
  "type": "statistic-cookie",
  "percentage": 40
}
```

and the following content:

```org
[1/2]
```

Yields:

```json
{
  "type": "statistic-cookie",
  "current": 1,
  "total": 2
}
```


### `Keyword`

```idl
interface Keyword <: Object {
  type: 'keyword'
  key: string
  value: string?
}
```

**Keyword** represents a keyword.

A `key` field must be present.
It represents the key of the keyword.

A `value` field can be present.
It represents the value of the keyword.

for example, the following org:

```org
#+TITLE: Hello World
```

Yields:

```json
{
  "type": "keyword",
  "key": "TITLE",
  "value": "Hello World"
}
```


### `Affiliated Keyword`

```idl
interface AffiliatedKeyword <: Object {
  type: 'affiliated-keyword'
  key: string?
  value: string?
  backend: string?
  options: string?
}
```

**Affiliated Keyword** represents an affiliated keyword.
It has three variants: `#+KEY: VALUE`, `#+KEY[OPTVAL]: VALUE `, and `#+attr_BACKEND: VALUE`.

A `key` field can be present.
It represents the key of the affiliated keyword.

If `key` field is present, an `options` field can be present.
It represents the options of the affiliated keyword.

A `backend` field can be present.
It represents the attributes the export backend used.

Either `key` or `backend` field must be present.

A `value` field must be present.
It represents the value of the affiliated keyword.

for example, the following org:

```org
#+name: image-name
```

Yields:

```json
{
  "type": "affiliated-keyword",
  "key": "name",
  "value": "image-name"
}
```

and the following org:

```org
#+name[options]: image-name
```

Yields:

```json
{
  "type": "affiliated-keyword",
  "key": "name",
  "options": "options",
  "value": "image-name"
}
```

and the following org:

```org
#+attr_html: :width 100px
```

Yields:

```json
{
  "type": "affiliated-keyword",
  "backend": "html",
  "value": ":width 100px"
}
```

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

### `Affiliated Keywords`

```idl
interface mixin AffiliatedKeywords {
  afflicated: [AffiliatedKeyword?]
}
```

**Affiliated Keywords** represents a list of [Affiliated Keyword](#affiliated-keyword) objects that attached to an [Element](#element).

A `affiliated` field can be present.
It represents a list of [Affiliated Keyword](#affiliated-keyword) objects.

for example, the following orgmode documents a link with affiliated keywords:

```org
#+name: image-name
#+caption: This is a caption for
#+caption: the image linked below
[[file:some/image.png]]
```

Yields:

```json
{
  "type": "link",
  "subType": "regular",
  "resourceType": "protocol",
  "rawLink": "file:some/image.png",
  "path": "some/image.png",
  "affiliated": [
    {
      "type": "affiliated-keyword",
      "key": "name",
      "value": "image-name"
    },
    {
      "type": "affiliated-keyword",
      "key": "caption",
      "value": "This is a caption for the image linked below"
    }
  ]
}
```

### Planable

```idl
interface mixin Planable {
  planning?: Planning
}
```

**Planable** represents a planning object that attached to an [Element](#element). By default, only [Headline](#headline) is allowed.

for example, the following orgmode documents a headline with a planning:

A `planning` field can be present.
It represents a [Planning](#planning) object.

```org
* TODO
  <Scheduled: 2020-01-01>
```

Yields:

```json
{
  "type": "headline",
  "level": 1,
  "todoKeyword": "TODO",
  "planning": {
    "type": "planning",
    "scheduled": {
      "type": "scheduled",
      "start": "2020-01-01T00:00:00.000Z"
    }
  }
}
```

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
