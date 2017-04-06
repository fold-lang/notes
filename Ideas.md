
@fold

- for the type annotations use the module itself (interface_of).
- The range function from Fold Elements may be defined as ^ operator. Which is used in Perl6 and is (kind of) similar to iota from APL.Ex: ^5 == [0, 1, 2, 3, 4]
- `f x y z` is a list. A list is a sequence of tokens. Not only by parenthesis as in lisp but by any operator: `f x y + 2` creates a list of `f x y`.
- Close a module? With module. Open in...
- Extend the definition of the expression type to include Atoms, Lists and Terms.
- Consider adding arity to functions (terms). Not a good idea – reason: currying, all the functions are unary.
- Have a syntax for items, pairs, binding.
- Optimize string concatenation.
- Organize the implicit functions in protocols. Implicit protocol/interface.
Use implicit namespace for function access. - Enum.filter would work on any 'a that implements Enum. Monad.bind too.
- A block is just a list of expressions not separated by commas.
- Polymorphic variants can be used as dict keys by encoding them as functions with pattern matching. Even better with lambda syntax: { `x => 2, `y => 5 }.
- The item or binding operator `=>` can be used to construct hash-maps: {2 => "two", 4 => "four"} and is also used for lambda functions.
- In this previous example for the separation of the bindings the comma is used, another alternative (not adequate for maps) is the pipe operator.
- For the ease of writing the comma may be omitted if the binding ends the line.
Mathematicas evaluation of unknown functions and values: f x. Symbolic applications. Fexpr.
- In the same way the semicolons can be omitted at the end of line in blocks, colons can be omitted in applications and commas can be omitted in lists: `f x : y ; z`
- Principles: emphasis flow – interruptless programming. Build on existing code, extend, improve.
- For blocks the `:` at the end of line could be omitted – results in a cleaner syntax for blocks.
- Uniform treatment of functions and functors.
- Boolean interface. if (x :: Boolean A).
- What if it fails?: `people find("Smith") firstName greet("George")` – bubble the error through the expression chain and make it return Result.

---

From APL to Fold:

```elm
avg ← {(+/ ω) ÷ ρ ω}
val avg = {(+/ ω) ÷ ρ ω}
val avg = ω -> (+/ ω) ÷ ρ ω
val avg = xs -> (+/ xs) ÷ ρ xs
val avg = xs -> (+/ xs) / ρ xs
val avg = xs -> (+/ xs) / # xs
val avg = xs -> (+/ xs) / # xs
val avg = xs -> (reduce (+) xs) / # xs
```

More examples:

```elm
-> ^4
:: [Int] = [1, 2, 3, 4]

-> ^4 \ 3
:: [Int] = [1, 2, 4]

-> ^5 \ [1, 4]
:: [Int] = [2, 3, 5]

-> ^5 \ [2..4]
:: [Int] = [1, 5]

-> 3 in ^5
:: Bool = T

-> A = [1, 2, 3, 4, 5]
-> B = [1, 5, 4, 7, 2]
-> fold (in B), ^(#A)
:: [Int] = [1, 5, 3, 2
```

- The real flexibility comes from connecting existing tools in useful ways.
  Fold environment should be able to call existing tools, such as executables,
  functions, library functions from other languages.
- DataFrames with SQL backend (sqlite3, pglib).
- Create a Dynamic Environment for the language with a clean and simple design inspired by the FoldingText editor. Show the evaluation results with a simple flat UI.
- The compiler should be smart enough to statically generate the most performant implementation. The inspiration comes from an article with a criticism of Go's channels, where the author claims that the hand-written implementation: yes!, but the question for Fold should be "Is it possible to generate code that is as good as a hand-written implementation?". Fold should avoid runtime abstractions at all cost and maximize the statical awareness. Another inspiration is the D programming language that pushes all possible decisions to the compile-time. The code can be reflected and adapted. It's not that the compiler does all the job, it's just that the programmer has to be able to inspect the code and make smarter decisions.
- The index operator `#` is used for horizontal data access while the `$` is used for vertical – column – access.
- Always include jokes and historical facts in Fold's documentation.
- Index in operation idea:
  - `{ "a": [(1, 2), (3, 4)]} # ("a", 0, 1) = 2`
- Compression operation idea:
  - `[1, 2, 3, 4, 5] # [T, F, T, F, T] = [1, 3, 5]`
  - `[1, 2, 3, 4, 5] # [0, 3, 4] = [1, 4, 5]`
  - `[1, 2, 3, 4, 5] # odd = [1, 3, 5]`
  - What if the keys in a map are lists?
  - Tree shaking, don’t use OCaml modules. Implement modules as records. Import only deps.
  - Dynamic scope for shell cmds. The argument format may be problematic with an automatic approach.
  - Boolean flags in the style of OCaml's optionals.
    - `print "Hello" ~should_flush`
- Use `<-` for bindings and `=` for equality. The monadic `do` block can still use `<-`.
- Can comma `,` be used as a function application operator?
- Control directives for exporting/hiding the type constructors/members outside the module.
- Use the macro syntax to define rewrite rules for optimisation:
  - `macro map f (map g list) = map (f << g) list`
- Define the translation from Fold to OCaml syntax in Fold.
- Very flexible and elegant way of macro definition is implicit lifting of `Expr` functions during the compilation phase. This means that macros are not special, they are just regular functions from `Expr -> Expr`.
- Explicit quoting of macro arguments is a first step for runtime staging.
- **Syntax types**: expressions, patterns, types, *etc*. – are all different classes of syntax. Allow the user to create new classes by combining existing classes. For example, assignment, list, pair, *etc*. are possible classes. Inspiration from a paper about Honu.
- `Option.assert :: Self a -> a` or `enforce`.
