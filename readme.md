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
    - [`Heading`](#heading)
    - [`Planning`](#planning)
    - [`Timestamp`](#timestamp)
    - [`Clock`](#clock)
    - [`DiarySexp`](#diarysexp)
    - [`Section`](#section)
    - [`Drawer`](#drawer)
    - [`PropertyDrawer`](#propertydrawer)
    - [`NodeProperty`](#nodeproperty)
    - [`Paragraph`](#paragraph)
    - [`Line Break`](#line-break)
    - [`HorizontalRule`](#horizontalrule)
    - [`Text`](#text)
    - [`TextMarkup`](#textmarkup)
    - [`Subscript`](#subscript)
    - [`Superscript`](#superscript)
    - [`LaTeXEnvironment`](#latexenvironment)
    - [`LaTeXFragment`](#latexfragment)
    - [`Entity`](#entity)
    - [`InlineTask`](#inlinetask)
    - [`PlainList`](#plainlist)
    - [`ListItem`](#listitem)
    - [`Table`](#table)
    - [`TableRow`](#tablerow)
    - [`TableCell`](#tablecell)
    - [`CenterBlock`](#centerblock)
    - [`DynamicBlock`](#dynamicblock)
    - [`CommentBlock`](#commentblock)
    - [`ExampleBlock`](#exampleblock)
    - [`ExportBlock`](#exportblock)
    - [`SourceBlock`](#sourceblock)
    - [`VerseBlock`](#verseblock)
    - [`Comment`](#comment)
    - [`FixedWidth`](#fixedwidth)
    - [`Link`](#link)
      - [`Radio Link`](#radio-link)
      - [`Plainlink`](#plainlink)
      - [`AngleLink`](#anglelink)
      - [`RegularLink`](#regularlink)
    - [`Target`](#target)
    - [`RadioTarget`](#radiotarget)
    - [`FootnoteReference`](#footnotereference)
    - [`FootnoteDefinitions`](#footnotedefinitions)
    - [`Citation`](#citation)
    - [`CitationStyle`](#citationstyle)
    - [`CitationReference`](#citationreference)
    - [`Macro`](#macro)
    - [`ExportSnippet`](#exportsnippet)
    - [`BabelCall`](#babelcall)
    - [`InlineBabelCall`](#inlinebabelcall)
    - [`InlineSourceBlock`](#inlinesourceblock)
    - [`StatisticCookie`](#statisticcookie)
    - [`Keyword`](#keyword)
    - [`AffiliatedKeyword`](#affiliatedkeyword)
  - [`Mixin`](#mixin)
    - [`Resource`](#resource)
      - [File](#file)
      - [Protocol](#protocol)
      - [ID](#id)
      - [Custom ID](#custom-id)
      - [Code Ref](#code-ref)
      - [Fuzzy](#fuzzy)
    - [`AffiliatedKeywords`](#affiliatedkeywords)
    - [Planable](#planable)
  - [`Content Model`](#content-model)
    - [`ElementContent`](#elementcontent)
    - [`GreaterElementContent`](#greaterelementcontent)
    - [`LesserElementContent`](#lesserelementcontent)
    - [`ObjectContent`](#objectcontent)
    - [`StandardSetObjectContent`](#standardsetobjectcontent)
    - [`MinimalSetObjectContent`](#minimalsetobjectcontent)
    - [TableCellContent](#tablecellcontent)
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

omast relates to [Dart][dart] and [javascript][javascript] in that it has a rich ecosystem of utilities for working with compliant syntax trees in JavaScript. However, omast is not limited to Dart and Javascript, and can be used in other programming languages.

## Nodes

### `Parent`

```idl
interface Parent <: UnistParent {
  children: [ElementContent | ObjectContent]
}
```

**Parent** ([UnistParent][dfn-unist-parent]) represents an abstract interface in omast containing other nodes (said to be children).
Its content is limited to only either [ElementContent](#elementcontent) or [Object](#objectcontent).

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
interface Timestamp <: Node {
  type: 'timestamp'
  subType: 'diary' | active' | 'inactive' | 'active-range' | 'inactive-range'
  start: Date
  end: Date?
  repeater: String?
  warning: String?
}
```

**Timestamp** ([Node][dfn-node]) represents a timestamp.

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

```idl
interface Clock <: Node {
  type: 'clock'
  timestamp: Timestamp?
  duration: string?
}
```

**Clock** ([Node][#dfn-node]) represents a clock to track time spent on a task. It is normally used in a special drawer in a heading that the name is LOGBOOK.

A `timestamp` field can be present.
It represents the inactive timestamp or inactive range timestamp of the clock. See [Timestamp](#timestamp).

A `duration` field can be present.
It represents the duration of the clock. the pattern of the duration is `HH:MM`.

for example, the following content:

```org
clock: [2024-10-12]
```

Yields:

```json
{
  "type": "clock",
  "timestamp": {
    "type": "timestamp",
    "subType": "inactive",
    "start": "2024-10-12T00:00:00.000Z"
  }
}
```

and the following content:

```org
clock: [2017-04-05 Wed 16:42]--[2017-04-05 Wed 16:52] =>  0:10
```

Yields:

```json
{
  "type": "clock",
  "timestamp": {
    "type": "timestamp",
    "subType": "inactive-range",
    "start": "2017-04-05T16:42:00.000Z",
    "end": "2017-04-05T16:52:00.000Z"
  },
  "duration": "00:10"
}
```
### `DiarySexp`
### `Section`

```idl
interface Section <: Parent {
  type: 'section'
  children: [ElementContent]
}
```

**Section** ([Parent](#parent)) contain one or more non-heading elements.

**Section** can contain any of [ElementContent](#elementcontent).

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
    }
  ]
}
```

### `Drawer`

```idl
interface Drawer <: Parent {
  type: 'drawer'
  name: string
  children: [GreaterElementContent]
}
```

**Drawer** ([Parent](#parent)) represents a drawer that contains arbitrary content associated with an entry.

**Drawer** can contain any of [GreaterElementContent](#greaterelementcontent).

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

### `PropertyDrawer`

```idl
interface PropertyDrawer <: Drawer {
  type: 'property-drawer'
  children: [NodeProperty?]
}
```

**PropertyDrawer** ([Drawer](#drawer)) represents a drawer that contains properties attached to a [Heading](#heading) or [InlineTask](#inlinetask) or a special [Section](#section) called zero section.

A `children` field must be present and can only contain [NodeProperty](#nodeproperty).

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

### `NodeProperty`

```idl
interface NodeProperty <: Node {
  type: 'node-property'
  name: string
  value?: string
}
```

**Node Property** ([Node][dfn-node]) represents a node property and can only exists in [PropertyDrawer](#propertydrawer).

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
  children: [StandardSetObjectContent]
}
```

**Paragraph** ([Parent](#parent)) is the default element, which means that any unrecognized context is a paragraph.

**Paragraph** can contain any of [StandardSetObjectContent](#standardsetobjectcontent).

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
### `HorizontalRule`

```idl
interface HorizontalRule <: Node {
  type: 'horizontal-rule'
}

HorizontalRule includes AffiliatedKeywords
```

**HorizontalRule** ([Node](#dfn-node)) represents a horizontal rule.

**HorizontalRule** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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

```idl
interface Text <: Literal {
  type: 'text'
}
```

**Text** ([Literal](#literal)) is any string that doesn’t match any other [ObjectContent](#objectcontent) can be considered a plain text object.

For example, the following orgmode:

```org
Alpha bravo charlie.
```

Yields:

```js
{type: 'text', value: 'Alpha bravo charlie.'}
```
### `TextMarkup`

```
interface TextMarkup <: Literal {
  type: 'bold' | 'italic' | 'underline' | 'verbatim' | 'code' | 'strike-through'
}
```

[Text](#Text) ([Literal](#literal)) can be emphasized. There are six text markup objects to emphasis a text as follows:

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
interface Subscript <: Literal {
  type: 'subscript'
  value: string
}
```

**Subscript** ([Literal](#literal)) represents a subscript.

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
interface Superscript <: Literal {
  type: 'superscript'
  value: string
}
```

**Superscript** ([Literal](#literal)) represents a superscript.

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
### `LaTeXEnvironment`

```idl
interface LaTeXEnvironment <: Node {
  type: 'latex-environment'
  name: string
  value: string
}

LaTeXEnvironment includes AffiliatedKeywords
```

**LaTeXEnvironment** ([Node][dfn-node]) represents a LaTeX environment.

**LaTeX Environment** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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
### `LaTeXFragment`

```idl
interface LaTeXFragment <: Node {
  type: 'latex-fragment'
  name?: string
  value: string
}
```

**LaTeXFragment** ([Node](#dfn-node)) represents a LaTeX fragment.

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
### `InlineTask`

```idl
interface InlineTask <: Heading {
  type: 'inline-task'
}
```

**InlineTask** ([Heading](#heading)) represents an inline task.

for an example, see [Heading](#heading).
### `PlainList`

```idl
interface PlainList <: Parent {
  type: 'plain-list'
  subType: 'unordered' | 'ordered' | 'descriptive'
  children: [PlainList | ListItem]
}

PlainList includes AffiliatedKeywords
```

**PlainList** ([Parent](#parent)) represents a list of items.

**PlainList** includes the mixin [AffiliatedKeywords](#affiliated-keywords).

**PlainList** can contain [PlainList](#plainlist) and [ListItem](#listitem).

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
### `ListItem`

```idl
interface ListItem <: Parent {
  type: 'list-item'
  bullet: string
  counterSet?: string
  checkedBox?: 'on' | 'off' | 'trans'
  tag?: [string]
  children: [ListItemContent]
}
```

**ListItem** ([Parent](#parent)) represents an item of a [PlainLsit](#plainlist).

A `bullet` field must be present.
It represents the bullet of the item. the value is either asterisk (*), hyphen (-), or plus sign (+) or a number or a single letter (a-z)..

A `counterSet` field can be present.
It represents the counter set of the item.

A `checkedBox` field can be present.
It represents the state of the checkbox of the item.

A `tag` field can be present.
It represents the tag of the item.

For an example of a list item, see [PlainList](#plainlist).
### `Table`

```idl
interface Table <: Parent {
  type: 'table'
  tblFm: string?
  children: [TableRow]
}

Table includes AffiliatedKeywords
```

**Table** ([Parent](#parent)) represents a table.

**Table** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

**Table** can contain [TableRow](#tablerow).

A `tblFm` field can be present.
It represents the formulas of a table.

for example, the following content:

```org
| Name  | Phone | Age |
|-------+-------+-----|
| Peter |  1234 |  17 |
| Anna  |  4321 |  25 |
```

Yields:

```json
{
  "type": "table",
  "children": [
    {
      "type": "table-row",
      "children": [
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "Name"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "Phone"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "Age"
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "table-row",
      "children": [
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "Peter"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "1234"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "17"
                }
              ]
            }
          ]
        }
      ]
    },
    {
      "type": "table-row",
      "children": [
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "Anna"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "4321"
                }
              ]
            }
          ]
        },
        {
          "type": "table-cell",
          "children": [
            {
              "type": "paragraph",
              "children": [
                {
                  "type": "text",
                  "value": "25"
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```
### `TableRow`

```idl
interface TableRow <: Parent {
  type: 'table-row'
  children: [TableCell]
}
```

**TableRow** ([Parent](#parent)) represents a row of a table.

**TableRow** can be used where table content is expected.

**TableRow** can contain [TableCell](#tablecell).

For an example of a table row, see [Table](#table).
### `TableCell`

```
interface TableCell <: Parent {
  type: 'table-cell'
  children: [TableCellContent]
}
```

**TableCell** ([Parent](#parent)) represents a cell of a table.

**TableCell** can be used where **TableRow**([TableRow](#tablerow)) content is expected.

**TableCell** can contain [TableCellContent](#tablecellcontent).

For an example of a table cell, see [Table](#table).
### `CenterBlock`

```idl
interface CenterBlock <: Parent {
  type: 'center-block'
  children: [GreaterElementContent]
}
```

**CenterBlock** ([Parent](#parent)) represents a block of text that is centered.

**CenterBlock** can contain [GreaterElementContent](#greaterelementcontent).

for example, the following org:

```org
#+BEGIN_CENTER

#+BEGIN_SRC
first line
#+END_SRC

#+END_CENTER
```

Yields:

```json
{
  "type": "center-block",
  "children": [
    {
      "type": "source-block",
      "value": "first line"
    },
  ]
}
```
### `DynamicBlock`

```idl
interface DynamicBlock <: Parent {
  type: 'dynamic-block'
  name: string
  parameters: string?
  children: [GreaterElementContent]
}

DynamicBlock includes AffiliatedKeywords
```

**DynamicBlock** ([Parent](#parent)) represents a block of text that is generated dynamically.

**DynamicBlock** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

**DynamicBlock** can contain [GreaterElementContent](#greaterelementcontent).

for example, the following org:

```org
#+BEGIN: example
first line
#+END:
```

Yields:

```json
{
  "type": "dynamic-block",
  "name": "example",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "first line"
        }
      ]
    }
  ]
}
```
### `CommentBlock`

```idl
interface CommentBlock <: Parent {
  type: 'comment-block'
  children: [GreaterElementContent]
}

CommentBlock includes AffiliatedKeywords
```

**CommentBlock** ([Parent](#parent)) represents a block of text that is a comment. It can contain markup.

**CommentBlock** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

**CommentBlock** can contain [GreaterElementContent](#greaterelementcontent).

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
          "value": "first line\nsecond line\n"
        }
      ]
    }
  ]
}
```
### `ExampleBlock`

```idl
interface ExampleBlock <: Literal {
  type: 'example-block'
}

ExampleBlock includes AffiliatedKeywords
```

**Example Block** ([Literal](#literal)) represents a block of text that is an example. It is not subject to markup.

**Example Block** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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
### `ExportBlock`

```idl
interface ExportBlock <: Node {
  type: 'export-block'
  backend: string
  value: string
}

ExportBlock includes AffiliatedKeywords
```

**Export Block** ([Node][dfn-node])) represents a block of text that is exported to a backend.

**Export Block** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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
### `SourceBlock`

```idl
interface SourceBlock <: Node {
  type: 'source-block'
  language: string
  arguments: string
  value: string?
}

SourceBlock includes AffiliatedKeywords
```

**SourceBlock** ([Node][dfn-node]) represents a block of text that is source code.

**Source Block** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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
### `VerseBlock`

```idl
interface VerseBlock <: Literal {
  type: 'verse-block'
  value: string
}

VerseBlock includes AffiliatedKeywords
```

**Verse Block** ([Literal](#literal)) represents a block of text that is a verse. It is not subject to markup and reserve indents.

**Verse Block** includes the mixin [AffiliatedKeywords](#affiliatedkeywords).

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

```idl
interface Comment <: Literal {
  type: 'comment'
}
```

**Comment** ([Literal](#literal)) represents a comment.

for example, the following org:

```org
# the comment 1
# the comment 2
```

Yields:

```json
{
  "type": "comment",
  "value": "the comment 1\nthe comment 2"
}
```
### `FixedWidth`

```idl
interface FixedWidth <: Literal {
  type: 'fixed-width'
}

FixedWidth includes AffiliatedKeywords
```

**FixedWidth** ([Node](#dfn-node)) represents a fixed width area.

**FixedWidth** includes the mixin [AffiliatedKeywords](#affiliated-keywords).

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
interface Link <: Node {
  type: 'link'
  subType: 'regular' | 'radio' | 'angle' | 'plain'
  rawLink: string
  description?: string
}

Link includes AffiliatedKeyword
```

**Link** ([Node][dfn-node]) represents a hyperlink.

**Link** includes mixins **AffiliatedKeyword** ([AffiliatedKeyword](#affiliatedkeyword)).

A `subType` field must be present.
It represents the subtype of the link. There are four subtypes, which are: `regular`, `radio`, `angle`, and `plain`.

A `rawLink` field must be present.
It represents the raw link string, which is the link string before any processing.

A `description` field can be present.
It represents the description of the link.

#### `Radio Link`
#### `Plainlink`

```idl
interface PlainLink <: Link {
  type: 'link'
  subType: 'plain'
}

PlainLink includes Resource
```

**PlainLink** ([Link](#link)) represents plain-type links are links that are not surrounded by angle brackets.

**PlainLink** includes mixins **Resource** ([Resource](#resource)).

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
#### `AngleLink`

```idl
interface AngleLink <: Link {
  type: 'link'
  subType: 'angle'
}

AngleLink includes Resource
```

**AngleLink** ([Link](#link)) represents angle-type links are links that are surrounded by angle brackets.

**AngleLink** includes mixins **Resource** ([Resource](#resource)).

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
#### `RegularLink`

```idl
interface RegularLink <: Link {
  subType: 'regular'
}

RegularLink includes Resource
```

**Regularlinks**([Link](#link)) represents the most common type of link, and structured according to one of the following two patterns:
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
interface Target <: Literal {
  type: 'target'
}
```

**Target** ([Literal](#literal)) represents a target that a [**RegularLink**](#regularlink) links to. It is usually used to link to a specific location in the document as an internal link.

for example, the following content:

```org
<<important>>
```

Yields:

```json
{
  "type": "target",
  "value": "important"
}
```
### `RadioTarget`

```idl
Interface RadioTarget <: Parent {
  type: 'radio-target'
  children: [MinimalSetObjectContent]
}
```

**RadioTarget** ([Parent][#parent]) represents a radio target that a [**RadioLink**](#radiolink) links to.

A `value` field must be present.
It represents the value of the target and must be one of [MinimalSetObjectContent](#minimalsetobjectcontent).

for example, the following content:

```org
<<<*important* information>>>
```

Yields:

```json
{
  "type": "radio-target",
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
```
### `FootnoteReference`

```idl
interface FootnoteReference <: Node {
  type: 'footnote-reference'
  subType: 'standard' | 'inline' | 'anonymous'
  label: string?
  definition: StandardSetObjectContent?
}
```

**Footnote Reference** ([Node][dfn-node]) represents a footnote reference.

A `subType` field must be present.
It represents the subtype of the footnote reference.

If `label` is only present, it represents a standard footnote reference.

If `label` and `definition` are both present, it represents an inline footnote reference.

If `definition` is only present, it represents an anonymous footnote reference.

A `label` field can be present.
It represents the label of the footnote reference.

A `definition` field can be present.
It represents the definition of the footnote reference and must be one of [StandardSetObjectContent](#standardsetobjectcontent).

for example, the following content:

```org
[fn:1]
```

Yields:

```json
{
  "type": "footnote-reference",
  "subType": "standard",
  "label": "1"
}
```

and the following content:

```org
[fn:1:definition]
```

Yields:

```json
{
  "type": "footnote-reference",
  "subType": "inline",
  "label": "1",
  "definition": {
    "type": "paragraph",
    "children": [
      {
        "type": "text",
        "value": "definition"
      }
    ]
  }
}
```
### `FootnoteDefinitions`

```interface
interface FootnoteDefinition <: Parent {
  type: 'footnote-definition'
  label: string
  children: [ElementContent]
}

FootnoteDefinition includes AffiliatedKeywords
```

**FootnoteDefinition** ([Parent](#parent)) represents a footnote definition.

**FootnoteDefinition** includes mixins **AffiliatedKeywords** ([AffiliatedKeywords](#affiliatedkeywords)).

**FootnoteDefinition** contains [ElementContent](#elementcontent).

A `label` field must be present.
It represents the label of the footnote definition.

for example, the following content:

```org
[fn:1] A short footnote.
```

Yields:

```json
{
  "type": "footnote-definition",
  "label": "1",
  "children": [
    {
      "type": "paragraph",
      "children": [
        {
          "type": "text",
          "value": "A short footnote."
        }
      ]
    }
  ]
}
```
### `Citation`

```idl
interface Citation <: Node {
  type: 'citation'
  references: [CitationReference]
  citeStyle: CitationStyle
  globalPrefix: string?
  globalSuffix: string?
}
```

**Citation** ([Node][dfn-node]) represents a citation.

A `references` field must be present.

A `citeStyle` field can be present.
It represents the citation style and variant.

A `globalPrefix` field can be present.

A `globalSuffix` field can be present.

for example, the following content:

```org
[cite/t:see;@foo p. 7;@bar pp. 4;by foo]
```

Yields:

```json
{
  "type": "citation",
  "references": [
    {
      "type": "citation-reference",
      "key": "foo",
      "prefix": "p. 7",
    },
    {
      "type": "citation-reference",
      "key": "bar",
      "prefix": "pp. 4",
    }
  ],
  "citeStyle": {
    "type": "citation-style",
    "style": "t",
  },
  "globalPrefix": "see",
  "globalSuffix": "by foo"
}
```
### `CitationStyle`

```idl
interface CitationStyle <: Node {
  type: 'citation-style'
  style: string
  variant: string?
}
```

**CitationStyle** represents a citation style.

A `style` field must be present.

A `variant` field can be present.

for an example, see [Citation](#citation).
### `CitationReference`

```idl
interface CitationReference <: Node {
  type: 'citation-reference'
  key: string
  prefix: Object?
  suffix: Object?
}
```
**CitationReference** represents a reference a reference to an individual resource is given in a [Citation](#citation).

A `key` field must be present.
It represents a citation key identifying a reference in the bibliography.

A `prefix` field can be present.

A `suffix` field can be present.

Both `prefix` and `suffix` fields giving information for the citation but not included in a citation.

for an example, see [Citation](#citation).
### `Macro`

```idl
interface Macro <: Node {
  type: 'macro'
  name: string
  args: string?
}
```

**Macro** ([Node][dfn-node]) represents a macro.

A `name` field must be present.
It represents the name of the macro.

A `args` field can be present.
It represents the arguments of a macro.

for example, the following content:

```org
{{{one_arg_macro(1)}}}
```

Yields:

```json
{
  "type": "macro",
  "name": "one_arg_macro",
  "args": "1"
}
```
### `ExportSnippet`

```idl
interface ExportSnippet <: Node {
  type: 'export-snippet'
  backend: string
  value: string
}
```

**ExportSnippet** ([Node][dfn-node]) represents an export snippet.

A `backend` field must be present.
It represents the backend of the export snippet.

A `value` field must be present.
It represents the value of the export snippet.

for example, the following content:

```org
@@HTML::width 1@@
```

Yields:

```json
{
  "type": "export-snippet",
  "backend": "HTML",
  "value": ":width 1"
}
```
### `BabelCall`

```idl
interface BabelCall <: Node {
  type: 'babel-call'
  name: string
  args: string?
  argsInHeader: string?
  argsInEnd: string?
}
```

**BabelCall** ([Node][dfn-node]) represents a babel call.

A `name` field must be present.
It represents the name of the name of a code block.

A `args` field can be present.
It represents the arguments of a babel call.

A `argsInHeader` field can be present.
It represents the arguments in the header of a code block.

A `argsInEnd` field can be present.
It represents the arguments in the end of a code block.

for example, the following content:

```org
#+CALL: double(n=4)
```

Yields:

```json
{
  "type": "babel-call",
  "name": "double",
  "args": "n=4"
}
```
### `InlineBabelCall`

```idl
interface InlineBabelCall <: BabelCall {
  type: 'inline-babel-call'
}
```

**InlineBabelCall** ([BabelCall](#babelcall)) represents an inline babel call.

for example, the following content:

```org
call_double(n=4)
```

Yields:

```json
{
  "type": "inline-babel-call",
  "name": "double",
  "args": "n=4"
}
```
### `InlineSourceBlock`

```idl
interface InlineSourceBlock <: Node {
  type: 'inline-source-block'
  language: string
  value: string
}
```

**InlineSourceBlock** ([Node][dfn-node]) represents an inline source block.

for example, the following content:

```org
src_emacs{(+ 1 2)}
```

Yields:

```json
{
  "type": "inline-source-block",
  "language": "emacs",
  "value": "(+ 1 2)"
}
```
### `StatisticCookie`

```idl
interface StatisticCookie <: Node {
  type: 'statistic-cookie'
  percentage?: number
  current?: number
  total?: number
}
```

**StatisticCookie** ([Node][dfn-node]) represents a statistic cookie in a [Heading](#heading) that usually used to counts any TODO entries or checkbox in the heading's children.

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
interface Keyword <: Node {
  type: 'keyword'
  key: string
  value: string?
}
```

**Keyword** ([Node][dfn-node]) represents a keyword.

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


### `AffiliatedKeyword`

```idl
interface AffiliatedKeyword <: Node {
  type: 'affiliated-keyword'
  key: string?
  value: string?
  backend: string?
  options: string?
}
```

**AffiliatedKeyword** ([Node][dfn-node]) represents an affiliated keyword.
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

### `AffiliatedKeywords`

```idl
interface mixin AffiliatedKeywords {
  afflicated: [AffiliatedKeyword?]
}
```

**AffiliatedKeywords** represents a list of [AffiliatedKeyword](#affiliatedkeyword) objects that attached to an [Element](#element).

A `affiliated` field can be present.
It represents a list of [AffiliatedKeyword](#affiliatedkeyword) objects.

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

## `Content Model`

Each node in omast falls into one or more categories of Content that group nodes with similar characteristics together.

See [Objects and Elements](https://orgmode.org/worg/org-syntax.html#orgd09136d) for more information.

### `ElementContent`

```idl
type ElementContent = GreaterElementContent | LesserElementContent
```

**ElementContent** are syntactic components that exist at the same or greater scope than a [Paragraph](#paragraph).

### `GreaterElementContent`

```idl
type GreaterElementContent = CenterBlock | QuoteBlock | Drawer | PropertyDrawer | DynamicBlock | FootnoteDefinition | InlineTask | Item | PlainList | Table | LesserElementContent
```

**GreaterElementContent** ([Element](#element)) is a container can contain directly any [GreaterElementContent](#greaterelementcontent) or [LesserElementContent](#lesserelementcontent).

### `LesserElementContent`

```idl
type LesserElement =  CommentBlock | ExampleBlock | ExportBlock | SourceBlock | VerseBlock | Clock | DiarySexp | Planning | Comment | FixedWidth | HorizontalRule | Keyword | AfflicatedKeyword | LatexEnvironment | NodeProperty | Paragraph | TableRow
```

**LesserElementContent** ([ElementContent](#elementcontent)) cannot contain any other elements.

### `ObjectContent`

```idl
type ObjectContent = StandardSetObjectContent | MinimalSetObjectContent | CitationReference | TableCell
```

**ObjectContent** is syntactic component that exist with a smaller scope than a paragraph, and so can be contained within a paragraph.

It has two useful sets to reference common objects: *standard set* ([StandardSetObjectContent](#standardsetobjectcontent)) and *minimal set* ([MinimalSetObjectContent](#minimalsetobjectcontent)).

### `StandardSetObjectContent`

```idl
type StandardSetObjectContent =  MinimalSetObject | ExportSnippet | FootnoteReference | Citation | InlineBabelCall | InlineSourceBlock | LineBreak | Link | Macro | StatisticsCookie | Target | RadioTarget | Timestamp
```

### `MinimalSetObjectContent`

```idl
type MinimalSetObjectContent = PlainText | TextMarkup | Entity |LatexFragment | SuperScript | SubScript
```

### TableCellContent

A minimal set of objects , citations, export snippets, footnote references, links, macros, radio targets, targets, and timestamps.

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
