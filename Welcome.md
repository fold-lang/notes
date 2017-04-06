
# Fold – Welcome

Fold is a pragmatic strongly-typed functional language with expressive,
programmable syntax for development of blazingly fast and robust applications.


## Motivation

Fold stands on the shoulders of giants. OCaml's compiler toolkit is used to
produce efficient native binaries or optionally bytecode executables, that
offer fast compilation times (useful for testing and prototyping). Being
compatible with OCaml also gives Fold access to the OPAM ecosystem with more
than a thousand of packages. The core part of the language is a powerful
macro-system inspired by Lisp, which gives the programmer the ability to grow
the language adapting it to different problem domains and integrate
compile-time computations to increase the runtime performance. One less
technical aspect of the language is its focus on development experience and
simplicity, lessons learnt from languages like Python and Elm.


Fold was designed with the following guiding principles in mind:

- *Expressivity* is the fundamental goal of the language used to measure how
  complex systems can be described in simple terms.
- *Freedom* is given to the programmers to use, adapt and change the language
  to suit their needs. The language should not stand in programmers way, but
  must warn in case of inconsistent and unexpected results.
- *Functional style* is the preferred way to describe and implement systems.
  Pure functions, immutable data-structures and declarative logic are the
  default building elements of the language, but imperative, statefull
  programming can and should be used when it makes sence.
- *Robustness* is achieved with the type-system and by describing the problem
  domain with union types and structural invariants. Program consistency in
  Fold is enforced by the compiler during compilation, avoiding expensive type
  checks in runtime.


## History

I started working on Fold at the end of 2014 having spent around 3 month
programming in OCaml. Learning a functional language was a very gratifying
experience because I was already exploring functional style in Python, using
the `itertools` module and trying to reduce mutable state in my programs.

Functional programming is characterised by composition and flow of data. In
imperative languages code is fragmented and sequential. In functional languages
data flows through a series of transformations to compute the final result.

Although I enjoyed working with OCaml I missed a few things from Python. Namely
Python has a very lightweight feel – the language disappears and you can focus
on solving your problem. But with OCaml things were different, there was always
something interrupting me: incomplete standard library, minor syntax annoyances
and inconsistencies, broken tools. I understand that I wasn't experienced
enough with the language (even after a few month), but it just demonstrated how
OCaml is not beginner friendly.

At the same time I studied other programming languages such as D, Haskell,
Julia, Rust, Clojure, Elixir, etc. One dominant feature that I liked about
these languages was their metaprogramming capabilities. Specially D, using
template metaprogramming, is able to produce very efficient code by eliminating
high-level abstractions and producing highly specialized code.

I decided to build Fold because I felt that OCaml could benefit from a nicer,
beginner-friendly syntax and a more powerful macro-system. I tried to design a
language as robust as OCaml, as nice as Python and as powerful as D.

Another important event was the release of Reason in late 2016 – a programming
language from Facebook based on exactly the same idea as Fold. Reason is OCaml
with a different, more JavaScript-like syntax. I decided to continue my work on
Fold even after Reason's release because I think that JavaScript's syntax is
not the most desirable one for a functional language and because Reason doesn't
offer a macro-system.


## Implementation

Fold is not a standalone language, it is implemented as a an alternative
extensible frontend for the OCaml compiler. This means that the language has
its own parser, abstract syntax tree and macro evaluation system, but the
actual compilation to executable code is done by the OCaml backend.

Before introducing all the parser details a few examples will be shown to
demonstrate the metaprogramming capabilities of the language.

Very few language constructs are implemented by the parser. Most of the syntax
is implemented as a library. The _if-expression_ is defined based on an
existing language construct:

```
-- Extend the grammar to include the if-expression.
syntax `if` test `then` consequence `else` alternative =
  case $test
  | True  -> $consequence
  | False -> $alternative
  end

-- Now that the if-expression is defined we can use it!
val result =
  if Random.int () > 100 then "too big"
  else "too small"
```

Syntax for builtin data-structures, such as lists, can be defined as:

```
rule items = expression (`,` expression)*

syntax list
  | `[` `]`               = List.empty
  | `[` (xs <- items) `]` = List.create xs
```

The equivalent BNF grammar for the previous example is:

```
items ::= expression | expression ',' items
list  ::= '[' items ']'
```

The parsing notation used in Fold is based on [Parsing expression
grammar](https://en.wikipedia.org/wiki/Parsing_expression_grammar). It is
implemented as an extension to [Pratt
Parser](https://en.wikipedia.org/wiki/Pratt_parser). The novel feature of Fold
is the combination of this two models. Here is a list of articles useful to understand them:


- [Pratt Parsers: Expression Parsing Made Easy](http://journal.stuffwithstuff.com/2011/03/19/pratt-parsers-expression-parsing-made-easy/)
- [Top-Down operator precedence parsing](http://eli.thegreenplace.net/2010/01/02/top-down-operator-precedence-parsing)
- [Growing a Syntax](https://www.cs.utah.edu/~rafkind/papers/fool09.pdf)


* * *

*NOTE*: I will continue the description of the internals of the compiler over
the following days.

