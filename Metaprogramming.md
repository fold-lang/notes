# Metaprogramming

This document explores the metaprogramming mechanisms present in Fold and discusses their implementation details.

## Approaches

- Templates
- Staged programming
- Macro systems
- Introspection


## Motivation

Before starting the definition of the metaprogramming mechanisms in Fold, the
main use cases will be studied. In which situations metaprogramming is useful
and why?

- **Language extensions / DSLs**: used to create new language constructs and
  define DSLs for isolated programming domains. In particular in Fold, the
  language is built as a library based on the canonical syntax. Ex: create the
  `unless` control structure; write a powerful DSL for testing.
- **Code generation, mixins**: write code that writes code to reduce code
  boilerplate and avoid repetition and errors. May be used more flexibly with
  static data from a JSON file for example. Ex: generate an API with a
  configuration file at compile-time, generate an automatic implementation for
  an interface.
- **Rewrite rules**: are user-defined optimisation rules that replace known
  syntax patterns. Ex: replace equivalent code fragments, stream fusion,
  compile-time memoisation.
- **Multi-stage, generative programming**: enables the generation of
  specialised expanded code that avoids the general run-time overhead.
- **Introspection**: used to inspect code or type information at compile-time
  for better decisions in other use cases. Ex: pattern match on a expression;
  specialise code for different input types; add attributes to all functions in
  a module (_e.g._ for tracing).


### Language Extensions

Fold is language implemented as a library. The compiler has a set of basic
parsing rules (described in the section about [Canonical
Syntax](#canonical-syntax)) and can be extended to include traditional
programming elements or domain-specific languages.


### Rewrite Rules, Stream Fusion and Deforestation

> Stream fusion is a way to avoid intermediate data structures when writing
> code in a functional style.

(<https://pavpanchekha.com/blog/stream-fusion.html>)

- <https://en.wikipedia.org/wiki/Deforestation_(computer_science)>

Rewrite rules operate on patterns and are not meant to introduce new syntax.
They manipulate existing definitions by rearranging or replacing them with a
new expression.

Rewrite rules can be applied to macros and thus need to be evaluated before
macro expansion.

One example rewrite rules is compile-time simplification of compound
operations:

```elm
List.map (x -> Int.to_string x)
  (List.map (x -> x + 10) [1, 2, 3, 4, 5])
```

In this example two lists are allocated, although one is intermediate and not
used. With rewrite rules it can be optimised by telling the compiler about the
following equivalence between expressions:

```elm
List.map f (List.map g l) -> List.map (f << g) l
```

This rule will be matched during the compilation and a rewrite will be
performed to compose the functions `f` and `g` and only produce one list.

***

## Design

The macrosystem for Fold should be simple and elementary providing building
blocks for different use cases. The base abstraction for code manipulation
should be a _function_, a function that takes and expression as an argument and
produces another expression as result. This imposes a limitation on the
definition of expressions: the abstract syntax tree should be as primitive as
possible to reduce breaking changes and include new syntax constructs.


## Implementation

The fundamental idea behind Lisp is its uniform syntax that works as a
primitive material for building complex abstractions. Fold appears to have a
regular grammar at first sight, but just underneath every syntactic construct
hides a _canonical syntax_, which gives it full freedom to extend and adapt the
language.


### Canonical Syntax

The canonical representation of every Fold program is the direct projection of
its abstract syntax tree, in which every expression is shown in prefix
notation.

A simple operation with an infix operator as:

```elm
val x = 2 + 2
```

Is translated during parsing to:

```elm
(= (val x) (2 + 2))
```

Here is the grammar of the canonical syntax:

```
expr = atom | form | (expr)
form = expr form
atom = ID | INT | FLOAT | BOOL | CHAR | STRING
```

