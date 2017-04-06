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
list  <- "[" "]" | "[" items "]" 
items <- expr ("," expr)*
```

```
type Rule =
  | Empty
  | Literal Literal
  | Sequence Rule Rule
  | Alternative Rule Rule
  | Many0 Rule
  | Many1 Rule
  | Optional Rule
  | Positive Rule
  | Negative Rule

macro (Sequence x (Many0 (Sequence "," x)))
macro (x (Many0 ("," x)))

  
XXX: LITERALS ARE VALID PATTERNS, but not types... :(
XXX: Constructors can be operators (eg ::).
XXX: Pattern views with infix constructors!

macro (x :: expression) (xs :: ((char ",") expression)*)
end
```


```
-- As an example consider the familiar `if` form.
-- We are want to parse the literal keywords and variable expressions.
-- This is a simple case without recursion.
if <condition> then <true_branch> else <false_branch>

-- Regular monadic parsing can be done using the `Parser` monad.
val if_then_else =
  symbol "if" >>
  expr >>= t ->
  symbol "then" >>
  expr >>= a ->
  symbol "else" >>
  expr >>= b ->
  undefined


-- Monadic notation improves the readability. We use it here since it is the
-- closest syntax we want for the macros.
val if_then_else =
  Parser.do
    symbol "if";
    let! t = expr in
    symbol "then";
    let! a = expr in
    symbol "else" >>
    let! b = expr in
    undefined
  end


-- One possible attempt is to encode the expression language as a type.
-- The variables include the constructor that will be used to generate the parser.
-- It is very limited since new macro rules can be added by the user and it is not
-- clear how the translation will work for custom types.
macro "if" (t :: Expr) "then" (a :: Expr) "else" (b :: Expr) =
  ...

-- On the opposite side we have rule bindings.
-- They are essential for constructing complex and recursive parsers.
rule Items = (x :: Expr) (xs :: ("," Expr)*)

-- Something similar to this...
-- But not exactly since this conflicts with basic pattern matching for atomic
-- parsers (not sequences) and 
macro
  | "[" "]" = List.empty
  | "[" (xs :: Items) "]" = xs

-- All of the above seems to be converging to the concept of views (_aka_
-- active patterns).
-- Let's start with something simple:
expr ("," expr)*

-- This of course encodes comma separated items.
-- What is expected from this formulation?
-- 1) attribution of variables;
-- 2) reference of existing rules (_i.e._ `expr` in this case);
-- 3) generation of an appropriate parser.

-- Variables could be easily added with `as`.
(expr as x) (("," expr)* as xs)

-- In this case `expr` and `("," expr)*` are used as patterns.
-- While in reality they are views.
-- It could be said that if the pattern is used directly it stands for an
-- expression:
x (("," expr)* as xs)

-- The language allows it but it looks inconsistent.
-- The next step is to use pattern views with an equivalent of `as`.

-- Checkpoints:
-- * macros are forms, _i.e._ lists
-- * views must operate on lists or individual items in lists


-- Here is how all macro views will look:
list_view :: List Expr -> ...


-- Using the ViewPatterns proposal for Haskell we get something like:
macro
  | "[" (items -> xs) "]" = xs
  | "[" "]" = List.empty
end


-- In order to keep this notation compatible with the origin monadic example we
-- can translate the previous to:
char '[' >> items >>= xs -> char ']'

-- Considering the types `items` should be of type `Parser`.
val items =
  expr >> many0 (char ',' >> expr)

-- Or with bound results that we need:
val items =
  expr >>= x -> many0 (char ',' >> expr) >>= xs -> [x & xs]

-- Using do notation.
val items =
  let! x  = expr
  let! xs = many0 (char ',' >> expr)
  in
    [x & xs]

-- Note: the call to `items` in the origin macro assumes that the
-- juxtaposition is PEG sequencing.

-- Note: using PEG-based notation for macros would work well but there's an
-- opportunity to implement PEG on top of views: use PEG syntax as part of
-- pattern views.

-- Now we can try to define the `if` view for lists of expressions.
pattern if_expr (t, a, b) =
  [Atom (_, Symbol "if"),   t,
   Atom (_, Symbol "then"), a,
   Atom (_, Symbol "else"), b]

-- The usage is syntaticaly distinct from regular patterns and looks like
-- function application.
function eval expr env =
  match expr
  | if_expr (t, a, b) ->
		...

-- Now we can adapt the definition of the list parser:
val list = char '[' >> items >>= xs -> char ']'
pattern ...


-- Considering the types `items` should be of type `Parser`.
val items =
  expr >> many0 (char ',' >> expr)


```

---

Macro rules in Fold are based on _Parsing Expression Grammar_ notation. In the
following sections I will show how it can be used to create simple and complex
syntax extensions for Fold.

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
