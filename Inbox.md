# Meta – Rationales


## Keyword arguments

The usage of keyword arguments communicates better the purpose of an argument. In Metaphor, keyword arguments are used even type signatures. In Haskell types are very important, people who program in Haskell spend a lot of time reading just type annotations. In an abstract sense following type ordering and transformations is enough to understand the concepts of the computation. But in the real world software, it is far more valuable being able to express the context of the computation.

-- Some complex Haskell code here with types
f :: X a => a -> (a -> a -> b) -> b -> a

A type theory guru will look at this an immediately understand that this functions ... while a real world programmer will just WTF?

f :: X a => input::a -> transformer::(a -> b -> a) -> accumulator::b -> result::a

Type annotations in signatures are optional.


- Types are meant to be read, not to be written. Metaphor compiler will happily write them for you.