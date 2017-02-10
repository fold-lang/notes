# PEG Combinators

Parsing combinators are implemented in popular functional libraries for flexible and efficient monadic parsing. _Parsing expression grammar_ is a formal model used to describe languages as a set of string-parsing rules. In this post I will describe a lightweight notation for monadic parsing combinators based on PEG. This notation will be used for macros in Fold.

---

```
val items =
  Parser.do
    let x  <- expr,
        xs <- many (char ',' >> expr)
    in
      return [x & xs]
  end

val list =
  Parser.do
    char '[' >> char ']' <|>
    char '[' >> items << char ']'
  end
```


```
list  <- "[" "]" / "[" items "]" 
items <- expr ("," expr)*
```

```
XXX: LITERALS ARE VALID PATTERNS, but not types... :(
XXX: Constructors can be operators (eg ::)
macro (x :: expression) (xs :: ((char ",") expression)*)
end


type Rule =
  | Empty
  | Token String
  | Sequence Rule Rule
  | Alternative Rule Rule
  | Many Rule
  | Many1 Rule
  | Optional Rule
  | AndPred Rule
  | NotPred Rule


```

---


```
An atomic parsing expression consists of:
- any terminal symbol,
- any nonterminal symbol, or
- the empty string ε.

Given any existing parsing expressions e, e1, and e2, a new parsing expression can be constructed using the following operators:
- Sequence: e1 e2
- Ordered choice: e1 / e2
- Zero-or-more: e*
- One-or-more: e+
- Optional: e?
- And-predicate: &e
- Not-predicate: !e
```

Using the proposed notation list literals could be defined this way:

```
macro items = expr ("," expr)*

macro
	| "[" "]" = quote List.empty
	| "[" items "]" = quote items
end
```
