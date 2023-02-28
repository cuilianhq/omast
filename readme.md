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

