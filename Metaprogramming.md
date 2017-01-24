# Metaprogramming

This document explores some metaprogramming mechanisms and discusses their integration in Fold.

## Approaches

- Templates
- Staged programming
- Macro systems
- Introspection


## Use cases

Before starting the definition of the metaprogramming mechanisms in Fold, the main use cases will be studied. In which situations metaprogramming is useful and why?

- **Language extensions / DSLs**: used to create new language constructs and define DSLs for isolated programming domains. In particular in Fold, the language is built as a library based on the canonical syntax. Ex: create the `unless` control structure; write a powerful DSL for testing.
- **Code generation, mixins**: write code that writes code to reduce code boilerplate and avoid repetition and errors. May be used more flexibly with static data from a JSON file for example. Ex: generate an API with a configuration file at compile-time, generate an automatic implementation for an interface. 
- **Rewrite rules**: are user-defined optimisation rules that replace known syntax patterns. Ex: replace equivalent code fragments, stream fusion, compile-time memoisation.
- **Multi-stage, generative programming**: enables the generation of specialised expanded code that avoids the general run-time overhead.
- **Introspection**: used to inspect code or type information at compile-time for better decisions in other use cases. Ex: pattern match on a expression; specialise code for different input types; add attributes to all functions in a module (_e.g._ for tracing).


## Design

The macrosystem for Fold should be simple and elementary providing building blocks for different use cases. The base abstraction for code manipulation should be a _function_, a function that takes and expression as an argument and produces another expression as result. This imposes a limitation on the definition of expressions: the abstract syntax tree should be as primitive as possible to reduce breaking changes and include new syntax constructs.


### Stream Fusion, Deforestation and Rewrite Rules

> Stream fusion is a way to avoid intermediate data structures when writing code in a functional style.

(<https://pavpanchekha.com/blog/stream-fusion.html>)

- <https://en.wikipedia.org/wiki/Deforestation_(computer_science)>

Rewrite rules operate on patterns and are not meant to introduce new syntax. They manipulate existing definitions by rearranging or replacing them with a new expression.

Rewrite rules can be applied to macros and thus need to be evaluated before macro expansion.

## Language Extensions

macro 


    macro time exp
        quote
            t = Time.now!
            x = %(exp)
            print "elapsed time: %(t - Time.now!) seconds"
            x
        end
    end
