
## Nov 4, 2014

- D-Expressions:
    - "Token macros become, in essence, part of an ad-hoc recursive descent parser and programmers must be aware of the implications of that. They must always take care to respect any macro syntax contained within the subexpressions manipulated because those subexpressions are not isolated within the structure."
    - This quote is very important given that I started the implementation of the Pratt parser in which the handlers only have access to the token stream. This may be a major design limitation. @to_review

- Map:
    - "∘ and ∙ are aliases for map and contramap respectively. Though, I only use those aliases for Functor instance of a function ((->) a), therefore I call them compose."
        - <http://stackoverflow.com/questions/14472310/pronounceable-names-for-scalaz-operators>

## Nov 5, 2014

- Evaluation precedence: the precedence of function application and algebraic expression must be carefully thought to minimize the need for parentheses. For example in OCaml even simple addition has lower precedence than application, which results in the restriction shown in the following code:

    ```
    # print_int 2 + 2
      ^ ~~~~~~~~~
    Error: This expression has type unit but an expression was expected of type                                   int
    ```


## Nov 17, 2014

- Macros as monads. Or code macro.

## Mixfix Operators

- Agda-style mixfix operators can be used to define the core language. So for example the simple conditional operation could be defined as:

    if : Bool -> then : a -> else : a -> a
    if true  then t else f = t
    if false then t else f = f


## Dec 15, 2014

- Create a dynamic poll with language features and the syntax.
- Organize the language library (lang.meta) by categories and for each one create a set of code examples with variants. Include a up-vote and down-vote button and a field for a small rationale (e.g. too long, too confusing, cryptic.), maybe even include a set of predefined options.


## Dec 26, 2014

### Pure Lambda Syntax

Prefix `!` will be used to denote the scope of the pure lambda expression.

    -> map !(~ / 2) [10, 20, 40, 30, 100]
     = [5, 10, 20, 15, 50] :: List Int

Instead of a more verbose and redundant:

    -> map (x -> x / 2) [10, 20, 40, 30, 100]
     = [5, 10, 20, 15, 50] :: List Int

The same `!` operator is used in postfix form to call nullary functions and to force application where ambiguity with optional arguments exists.

    -> hello (name = "World") = print "Hello, ~!" % name
    -> hello!
    "Hello, World!"

The standard solution to this problem is to use the unit argument to denote the end of application. But this reminds me of the `NULL` pointer convention used in C, which is fragile and confusing.

* * *

Another option for the prefix denotation is to use `~>`. This alternative has an advantage of being consistent with the label operator `~` and the regular lambda syntax:

    -> map ~>(~ / 2) [10 20 40 30 100]
     = [5 10 20 15 50] :: List[Int]

But the `!` is favoured for being more compact and because of its usage as application forcing operator. Also this way the language will have less arrow operators.


## Dec 28, 2014

Haskell programmers try to avoid parentheses because their usage in their language is inconsistent. In regular imperative programming languages you _always_ use parentheses to invoke a function, while in Haskell they are only required to force the precedence of the expression. When you think about programming in general, precedence handling is the last think you want to worry about while solving your problem. The consistent usage of parentheses (or generally any common syntax) became almost automatic and you stop noticing their presence. And this is a fact since all Lisp programmers say they can see through all the parentheses, and when they write them they don't think they are writing yet another parentheses in their code. Since in Haskell you usually start by writing without parentheses, when you need a pair of them your writing flow is interrupted and you need to start thinking about operator precedence and explicitly delimit your expression. Which, if you think about it, magnified by the number of times you do it, is really annoying. That's why `$` is so common in Haskell, because you write it to just tell the compiler that you want your expression to start exactly _here_ and span to the end of line. This reduces your effort by half, because you don't need to inform the compiler where the expression ends.


## Jan 1, 2015

How to handle sequences of expressions?

A program is a sequence of expressions, and a sequence is itself an expression.

    type expression =
        | Sequence of expression list
          ...

- The sequence can be explicitly built with the binary semi-colon operator: `a; b; 2 + 2`.
- On the other hand, a sequence operator is automatically inserted on a newline, except when a newline occurs in the middle of another expression.

    a = 2 + 2
    print a

Which is converted to:

    a = 2 + 2;
    print a

But not when the newline occurs in the middle of an expression, as in the following example, where the expression is parsed without changes:

    a = 2 +
        2


### Case Studies

How should be parsed a function application expression with a newline?

Regular function application:

-> sum 2 3
 = (sum 2 3)

Prefix function application with a new-line in the middle:

-> sum 2
       3
 = (sum 2 3)
 = (do (sum 2) 3)

Infix function application with a new-line in the middle:

-> 2
   + 3
 = (+ 2 3)
 = (do 2 (+ 3))

-> 2 +
   3
 = (+ 2 3)
 = (do (+ 2) 3)

-> map (x -> x + 1)
   [1, 2, 3]
 = (map (lambda [x] (+ x 1) [1, 2, 3])
 = (do (map (lambda [x] (+ x 1)) [1, 2, 3]))

-> if (a > 3) then:
       print "hey"
       "yes"
   else:
       2 + 2
       "no"

-> get "/main":
       print "Main view"
       m = load_model "main"
       render main_view model: m


### How other languages solve this problem?

In Go, there is a basic rule:  “if the newline comes after a token that could end a statement, insert a semicolon”.



## Jan 4, 2015

**Parsing function application**

Forms:

-> f a b c d
-> - a b c d

The prefix minus operator requires an argument:
-> - x

If `f` is a prefix function, then `f`, depending on the following tokens, may be treated both, as a literal and as a prefix function.

Consider the following situations, where f and g are functions, x and y are literals and (>>) and (+) are infix operators:

-> f
 = (name f)
-> x
 = (name x)
-> x + y
 = ()
-> f >> g
 = (apply (>>) [f g])
-> f x y
 = (apply f [x y])
-> f g x
 = (apply f [g h])
-> f (>>) x
 = (apply f [(>>) x])

x y z])


The main difference is the handler used for the token after `f`, where `>>` is a prefix operator (led) and x is just a literal (nud).

After a symbol token is


Any expression may be used as a prefix expression.

-> (x + y)  z
 = (apply (+) [x y])


-> (a > 3) ? "yes" : "no"


All the function applications are parsed the same way as the following expression with multiple prefix minus optriangle a b c))))

-> f x y z
 = (f (x (y (z))))

Alternatively the previous expression may be parsed as:

-> f x y z
 = (f [x y z])


-> f x y z + g
 = (f [x y z])



- Start parsing `a b c d` when rbp condition fails check the len and decide if its an application or an id.


## Jan 8, 2015 – Lambda Functions

-> \ x y -> x + y
-> \ 5
-> !(~ + ~)

-> map !(+ 2) [ ... ]


- A function may have distinct polymorphic implementations, called methods.

-> print


## 11 Jan, 2015

How the following expressions should be parsed:

-> let f a b = a + b in: f 3 2 - 1
 = 4



## 12 Jan, 2015

- About function application syntax:
    - To introduce a syntactic the keyword arguments may receive a special _block_ argument. sep::String) = ...



split with


## 18 Jan, 2015

- Error handling: each time an error occurs, store all parser state and pass it for analysis before firing an exception.


## 3 Feb, 2015

- Can the head of an expression be an arbitrary expression?

The regular form of a macro term is defined as a head, referencing a symbol, and a list of arguments, which are expressions.


## 4 Feb, 2015

- The expression block definition syntax with `:` for body introduction will not be used. It is inconsistent with type definitions, and is not adequate for function definitions.


type Person = { name :: String, age :: Int }
function sum x y = x + y
interface Comparable[a] = ...
module Comparable[int] = ...

not:

type Person: { name :: String, age :: Int }
function sum x y: x + y
interface Comparable[a]: ...
module Comparable[int]: ...


## 19 Feb, 2015

- Parsing applicative style function call with prefix parser able to take all the arguments until a led is found would simplify substantially the indentation handling.


## 14 Mar, 2015

Define the `Code` type, which extends `Expr` with types.

`(5 + 2) :: Code Int = (`+, [`5, `2])

- Study the type inference system of OCaml.
- Study the MetaOcaml code to see how it works.

___


## July 9, 2015

The syntactic elements must compose. The function definition, or any definition must not change the value.


## July 13, 2015

Fold – Syntax as a library.


## July 15, 2015

### Seq vs App

Fold syntax has a notion of sequence – a list of adjacent expressions separated by space. Consider some examples:

    f x y z
    1 2 3 4
    f x + g y

All this expressions contain sequences. Some of them may be function applications.

On which level should the separation be made?

- In the grammar definition: every function/macro creates a specialised parser with an appropriate arity. For example the unary minus (as in "-3") will be a function parser accepting a single null denotation expression; a function `f` parser accepting 3 arguments will check during parsing the number of provided arguments.
- The second approach is to build application/sequence expressions on the fly just by analysing the syntax. All expression introduced just with juxtaposition are converted to sequences. A distinction between function calls and other constructs is made during the further semantical processing.

The first method may be more intuitive for basic parsers, such as in the the unary minus example or macros (`let`, `open`, _etc_), but is not adequate for function application since the number of function arguments is not known during parsing.

Preliminar decision: use the second method, it is more general. A combination of two could be used but it would result in duplicate logic validation in separate compiler phases.


### How to append expressions?

Fix the appending of expressions. The current implementation is broken.

- Should `(f x) y` be equivalent to `(f x y)`?
- **Yes**. It is a basic precedence rule, the same way `(2 + 2) + 1` has redundant parentheses, any applicative expression can have them too: `f x y z` is equivalent to `((f x) y) z`.

There may be some cases where the described grouping rules for application are not intuitive. Consider the following example:

    -- Let's greet Alice in Portuguese!
    => (greetings_by_lang # "pt") "Alice"
    :: Str = "Olá, Alice!"
    -- This expression is syntactically equivalent to:
    => (# greetings_by_lang "pt" "Alice")
    :: Str = "Olá, Alice!"
    -- This may seem confusing but since `greetings_by_lang is
    -- a list of functions it works.

Here is an equivalent code in OCaml (`$` == List.nth).

    # let xs = [(fun x -> x + 1); (fun x -> x + 2);
                (fun x -> x + 3); (fun x -> x + 4)];;
    val xs : (int -> int) list = [<fun>; <fun>; <fun>; <fun>]
    # xs $ 1;;
    - : int -> int = <fun>
    # ($) xs;;
    - : int -> int -> int = <fun>
    # ($) xs 1;;
    - : int -> int = <fun>
    # ($) xs 1 2;;
    - : int = 4


## July 16, 2015

    List.nth     :: [a] -> a?
    List.nth_exn :: [a] -> a  (raises exception)

Any time the optional function is used a postfix operator `!` could be applied to raise exception when no value is available, just by forcing the optional value.

    => List.head
    :: [a] -> a?
    => List.head!
    :: [a] -> a
    => List.head (^10)
    :: Int? = Some 1
    => List.head! (^10)
    :: Int = 1

### Next Steps

- Improve the Grammar module. Create `Env.t` and generic definitions (for both macros and values).
- Create the explicit REPL phases.
- Define the basic demo operators within the language.
- Define a list of core operators (macro definition, _e.g._).
- Macro rewrite rules definition within the language as a DSL.
- Compile-time HTTP requests. Just joking... May be possible eventually though.


## July 17, 2015

- **What kind of data will be stored in the `Env`?** Unevaluated `Exp.t`, atoms or complex instructions

The literals that are stored in the scope only have `nud` parsers.

- Understand the difference between the typed and untyped macros. Start with untyped macros, _i.e._ simple AST substitutions (no guarantees that the generated code will be well-typed).

- The separation of default NUD and LED rules may result in better logic reuse. Example: The `if` parser, a regular NUD parser, may be used in LED context resulting in more flexible code:

    if ok then "yes" else "no" end
    print ("Answer: " ++ if ok then "yes" else "no" end)

- **Discard**. Does not make sense.


## July 21, 2015

Try to avoid the `~` symbol for named function application with a scope variable.

    default = 0
    Dict.get scores "Alice" default

    default = 0
    Dict.get scores "Alice" ~default

---

Instead of allowing the definition of infix operators with custom precedence
create a fixed precedence table for all the symbols that may start an operator.
This will avoid the need to specify the precedence in the base macros.

## July 23, 2015

- The addition of named arguments will be added after the first stable release.
- Named argument definition depends on the block definition. A simple `'function` macro introduces an implicit block.


## July 28, 2015

### Blocks

Some of the Fold's macros may implicitly introduce code blocks.
Some examples are `macro`, `function`, `interface`. The block is started after the EOL symbol is read on the same line where the macro begins.


    function sum x y<EOL>
        x + y
    end

**Discard**. Block parsing is integrated with general groupping. Simple `do-end` groups can be extended to create blocks by inspecting the first expression.

    do
        sum x y
        x + y
    end

In this example changing `do` to `function` and matching the first expression, i.e., `sum x y`, it is possible to directly create function blocks.

    function sum x y
        x + y
    end

This idea is implemented and tested in [80f9eeb](https://github.com/fold-lang/pratt-parser/commit/80f9eeb) for all kinds of blocks, with and without arguments.


### Macros

The basic idea behind syntax rules is to capture the static symbols and the code arguments that will be matched by the parser during the evaluation.

A simple if condition is a good example to illustrate this idea:

    if x > 3 then
        print "Ok"
    else
        print "No"
    end

In this case, the static symbols are: `if`, `then`, `else` and `end`. And the delimited expressions (`x > 3`, `print "Ok"`, _etc_.) are passed as compile-time variables to the macro:

    macro `if cond `then conseq `else alt `end

In the macro declarations this two entities must be distinguished. The static symbols, as opposed to the variables, do not need any additional information, such as the matched structure or type. Let's have a look at a familiar function definition macro:

    macro `function exp_list...

This macro is introduced by the static symbol `function`, followed by the spliced list of expressions, namely the function name and the arguments. So the previous rule may be further expanded to:

    macro `function (name & args)...

By applying pattern matching to the expression list it is easier to inspect the structure of the macro arguments and also validate and extract the required information.

As one may notice, the suggested syntax to distinguish between static symbols and expression variables is very simple: quoted names (just symbols) and in-line variables. This way the basic pattern matching on expressions can be used directly in the parser.

One alternative to this approach is to annotate the variables and use in-line symbols:

    macro $list [ $n ]

In this case the variables are explicitly introduced by the interrogation mark and the static symbols are written directly.

The limitations and the advantages of each one of the encodings will be studied during the implementation but for now the first one will be used.


## November 25, 2015

Use `~` for explicitly marking partially applied functions:


    hello name: "Alice" lang: "en"
    map (hello lang: "pt" ~) ["João" "Maria" "André"]

## November 27, 2015

`let` bindings are supported in Fold with more concise syntax:

    let
        x = 1 + 2
        y = x - 8
    in
        print x
    end

---

- Set grammar rules in the expr environment. Env supoorts both nud and led
  definitions.
- Minimize computation pqths with automata. How a computation graph can be
  defined as an FA?
- Protected scope for limited rules. Ex: `@` for as-patterns should be only
  available in the pattern.


## December 3, 2015

- One possible way of implementing a simple postfix syntax rule is to match the
  postfix operator during the eval on List xs. This has the advantage of having
  the same precedence of application lists.

- Discard. Postfix rules are already easily implemented withe the pratt parser.


## Fri Dec  4 01:45:51 WET 2015

Eval environment can be defined as a state monad:

  - <https://github.com/haskell-lisp/wyas/blob/master/src/Eval.hs>

The latest commit `9178d28` has a working implementation of the function
definition. An additional Func constructor had to be added to the Expr type.
The implementation of this constructor may not be final though. As an
alternative consider using a function from expr list to expr.

Next steps: refactor the definition of builtins, improve the Env management
(probably avoid returning it from eval) and implement the macro support.


## Tue Dec 15 02:03:36 WET 2015

The previously suggested definition of Func expression constructor as a
function will avoid the need of explicit env argument.

Env definitions will include the grammar rules from the language definition.


## Wed Jan 27 13:58:07 WET 2016

- To store closure scope environment, a genreic metadata value can be added to


### Syntax Trees

```fold
let x = 2 + 2

Term [Sym "="; Term [Sym "let"; Sym "x"]; Term [Sym "+"; Int 2; Int 2]]

Call [Sym "="; List [Sym "let"; Sym "x"]; Term [Sym "+"; Int 2; Int 2]]

```


## Thu Feb 18 02:43:29 WET 2016

- People are born at age 0, and durations start at time 0.
  - <http://www.jsoftware.com/papers/indexorigin.htm>


## Aug 17 2016

- I should find a way to merge Env and Grammar modules. How can the Rule definition be stored within the scope map?


## Aug 19 2016

- It is more important that the function definition looks similar to function call, rather than to lambda expression. Mainly because the named arguments will look strange separated from the function name.

Example:

```ocaml
move ~(from source) ~(to dest) =
  Unix.system ("mv {} {}" % (source, dest))

move = ~(from source) ~(to dest) ->
  Unix.system ("mv {} {}" % (source, dest))
```


## Aug 23 2016

Macro definition with braces is straightforward, since blocks can be seen as regular expressions.

```
macro `if test a `else b ...

line = Console.line ()
if (lengh (String.split by: ' ' line) == 0) {
  fail "Empty input. Please write at least two words."
} else {
  print "You wrote %(line)"
}

```

There is no need for the explicit `{...}` notation, blocks are already correctly parsed as an atomic or sequential expression.

The direct translation of this notation to the `do-end` syntax does not result in an acceptable solution because writing explicitly the `do` keyword is too verbose and conflicts with the possibility of its usage for the monadic blocks.

Here is how would the previous example look with this syntax:

```
macro `if test a `else b ...

line = Console.line ()
if (lengh (String.split by: ' ' line) == 0) do
  fail "Empty input. Please write at least two words."
end else do
  print "You wrote %(line)"
end
```

...Which is ridiculous.

The question is: _Is it possible to define a consistent rule for parsing blocks with the `end` noation and line terminations?_

```
macro `if test a `else b ...

line = Console.line ()
if (lengh (String.split by: ' ' line) == 0)<EOL>
  fail "Empty input. Please write at least two words."
else<EOL>
  print "You wrote %(line)"
end
```


## Aug 24 2016

One concrete limitation of the white-space sensitive syntax is the inability to compose blocks. The following construct is impossible with indentation-based if.

```
if flag 1 else 2 end + 2
```

***

### Macro Rules

```
(define macro (a (quote symbol +) b)
 (quote
    plus (unquote a)
         (unquote b)))

(a + b)

infix 7 +

Infix
  (fun left ->
    get >>= fun { token } ->
    consume token.value >>
    expression 7 >>= fun right ->
    return (Expr.Form [Expr.Atom token; left; right]))


```


```
if_then_else_ :: ...

if test then a else b = ...

`(if then else) :: ...

(if:then:else:) :: ...

(if..then..else..) :: ...
```


```
macro option `?` default =
  quote:
    case option:
      Some value -> value
      None       -> default


```


## Aug 30 2016

- Q: How are parsers/macros stored in the environment?
- A: They are not. Instead of storing the parser directly in the environment, an abstract expression description is bound to each definition. The _parser syntax_ is then interpreted into an actual monadic parser (see the `parse_rule` function).

From the previous answer we can extract a definition:

**Syntax Rule** – an expression that describes the syntax and is interpreted as a parser. Examples of syntax rules are: `if` macro, `+` sign.


## Sep 6 2016

Referencing


## Sep 13 2016

- Operator definition in Fold:
  - <https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html>



## Nov 19 2016

- Consider including symbol literals in the Fold’s lexer.
 
 
## Dec 3 2016

- **Q**: What are the advantages and tradeoffs of having fully custom syntax as compared to infix, prefix and postfix definitions?


### Grammar definition

    "if" test "then" a "else" b = …

    `if test `then a `else b = …
    
    


**Operators**:

    
    -- Option with default value.
    @infix 6
    _ ? _ :: Self a -> a -> a
    self ? default =
        case self {
            Some x -> x
            None   -> default
        }
    
    -- Monad signature
    sig Monad {
        type Self a
        pure :: a -> Self a
        _ >>= _ :: Self a -> (a -> Self b) -> Self b
    }
    
    -- Conditional macro
    @macro
    if _ then _ else _ :: Code Bool -> Code a -> Code a -> Code a
    if t then a else b =


## Dec 8 2016

- **Q**: How do you represent the list macro `[a, b, c, ...]`?

Provide multiple rules with common prefix.

    
    [ ]
    [ _ ]
    [ _ , rest... ]
    

### Named Application

Fold uses a lightweight mechanism to translate complex syntactic structures to regular function applications. This is achieved by the following set of rules:

	-- Regular function application.
	f x => f x
	
	-- Function application with a named argument
	f label: x => f label: x
	
	-- Function application with a named block argument
	f label
	  x
	  y
	end
		=> f label: (x; y)
		

## Dec 14 2016

```
case expr
  Form [Symbol "=", name, value] ->
    Env.add name (eval env value) env,
    Expr.unit
  Form (Symbol "=" & _) -> invalid ()
  Atom x -> Atom x
end

r = map (count (== 'a')) data_opt |>
    | Some x -> x + 1
    | None -> 0

```


## Dec 26 2016

### Goals

- **Expressiveness**
- **Performance**
- **Composition**
- **Consistency**
- **Experience**
- **Simplicity**

### Questions

- How should the type annotations work?
    - a) as in OCaml with inline type annotations and separate interfaces;
    - b) as in Haskell alongside definitions.

## Dec 27 2016

- **Decision**: Use whitespace insensitive syntax with ML-style definitions and explicit sequencing operator `;`.


## Dec 28  2016

StandardML uses functions with clauses for definitions with pattern matching. One example is shown here:

```
fun factorial 0 = 1
  | factorial n = n * factorial (n - 1)
```

This particular notation is very concise, expressive and elegant and allows the implementation of a whitespace insensitive parser with Haskell-like pattern-matching.

There are some limitations of this design if applied to Fold:

- Nested `let` definitions require the `fun` keyword.
- Commenting individual patterns becomes inconsistent because of the first match.


## Jan 4 2017

- Pattern matching for infix operators does not work well with lambda syntax.

```
def factorial 0 = 1
  | factorial n = n * factorial (n - 1)

def 0! = 1
  | n! = n * factorial (n - 1)
```

The second example is only possible with full function + pattern syntax.


## Jan 31 2017

Function application syntax in Fold follows the tradition started by the functional programming languages such as Lisp and Haskell: arguments are passed to functions by juxtaposition. This is different from the familiar mathematical notation _`f(x)`_ adopted by most programming languages.

The advantages of space separated argument application are:

- **Cleaner syntax**: this minimalistic approach results in code that many functional programmers consider elegant.
- **Easier metaprogramming**: the fundametnal part of the metaprogramming model in Lisp is the representation of lists in Lisp. Function application is just a special case of list literals in Lisp. This duality is an important design decision that ensures flexibility and consistenncy to the Lisp programms.
- **Explicit currying**: In Haskell the usage of juxtaposition for application is mainly motivated by the underlying function model in which all functions are only passed one argument. So a function call `f x y z` in reality is treated as `((f x) y) z`.


The disadvantages of the juxtaposition for application are:

- **Unfamiliar to beginers**: people starting with functional languages usually find this syntax strange. It is understandable since even the regular _`f(x)`_ notation is used in math.
- **Parentheses handling**: the regular syntax always forces the programmer to use the parenthese. It is consistent and pepole don't think about it. With functional syntax you only need parentheses if your argument is a compound expressions. In some situations it may be inconvenient to keep track of the parentheses and match them while writing nested applications. Haskell programmers use the `$` operator to avoid writing parentheses. In OCaml it is less frequent but pepole may use `@@` for the same reason.
