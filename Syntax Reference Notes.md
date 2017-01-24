## Comments

    -- Single-line comment
    
    ---
    Multi-line comments
    ---


## Canonical Syntax

### Atoms

**Booleans**

    True
    False


**Numeric**

    42
    1_000_000
    3.14


**Textual***

    'x'
    "abc"
    """
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse et tortor et lorem egestas mollis vel ac metus.
    Donec sit amet orci euismod quam mattis condimentum eu id purus. Phasellus vel augue accumsan, egestas metus tempus, fermentum purus.
    """


**Names**

    foo
    (+)
    `symbol
    `+


### Forms

Forms are structural elements that combine atoms and other forms into complex language constructs.

Inspite of the numerous language constructs present in Fold, all of them share the same underlying data structure.

To unveil this uniform representation let's analyse some code fragments.

```
calc box =
    let x = box.position.x
        y = box.position.y
    in:
        x * y

escape_earth velocity speed =
    let
        escape_velocity = 11.186
        orbital_speed   = 7.67
    in
        if
            velocity > escape_velocity -> "Godspeed"
            speed == orbital_speed     -> "Stay in orbit"
            otherwise                  -> "Come back"
        end
    end



print to: IO.stdin "Hello!"


if (n == 0)
    1
else
    n * factorial (n - 1)
```

All of the previous code examples are translated to the canonical syntax following a few simple parsing rules.

```
(let ((= a 42)
     (= b 3))
    (sqrt (4 * a * b) / 2))

(print (: to IO.stdin) "Hello!")

(if (n == 0)
  (: then 1)
  (: else (n * factorial (n - 1)))
```

To understand how all these examples are parsed, let's study their definition:

```
print (to as file = IO.stdout) (~end = "\n") str = ...

if condition then: a else: b =
  
```

The data structure that unifies all syntax constructs is:

```

pattern Statement
	| p::Pattern = e::Expr



type Syntax =
  | Expression
  | Statement
  | Pattern

let 
```


#### Edge Cases

**Infix operators and sequences**:

```
f x y
- 3

negate x =
  print "will negate $x"
  -x

-- Expected:
(define (negate x)
  (seq (print "will negate $x")
        (- x))
        
-- Actual:
(define (negate x)
  (- (print "will negate $x") x)

```


#### Named arguments

Arguments of the function calls can be optionally annotated with labels to improve readability and avoid mismatches in the arguments.

A function that draws a text widget at a specified coordinate can have the following type:

```
draw_text :: String -> Point -> ()
draw_text :: String -> at: Point -> ()
```

## Expressions

```
type Expr =
  | Atom Literal
  | Form [Expr]
  | Func [Expr] -> Expr
```

if :: Code Bool -> Code a -> Code a

For the definition of compound expressions `List` constructor is used. Aside from the primitive values all the syntactic elements are defined as lists. Function application in particular is also defined as a list expression, where the head of the list is the function (a function name or a lambda value) and the tail are the arguments.

```
(f x y z)
(+ 2 (- 6 4))
```


- Should a separate expression constructor exist for application? It may be called a _term_.

On the syntactic level there is no distinction of function application and space-separated lists.

----
## Element Access

**Goals**:

- Unified syntax for: Lists, Arrays, Vectors, Maps, Hash Tables, Matrices, Records, Custom indexable data types (JSON, Data Frames, _etc_), Modules (not a priority).
- Multiple selection strategies: single index, multiple indexes, slices, bitmaps, filter functions, etc.
- Reduce the use of macros to minimum. Every operator must have a named alternative.
- Don't overload operators with incompatible types.
- Keep the simplicity of the record access syntax: `record.field`.

```
-> letters = ['a', 'b', 'c', 'd']
-- (define letters (list 'a' 'b' 'c' 'd'))
:: List Char

-> # [0, 1, 2, 3, 4]
:: Int = 5

-> # [0, 1, 2;
      3, 4, 5]
:: Int = 6

-> # letters
-- length letters
-- length :: Collection a -> Int
:: Int = 4

-> letters @ 2
-- (get letters 2)
:: Char = 'c'

-> letters # 2
-- (get letters 2)

-> letters !! 2
-- (get letters 2)

-> letters[2]
-- (letters (list 2))

-> letters[0..2]
-- (letters (range 0 2))
 = []

```

### Access Strategies

- Get one element at index/key from a collection.
- Get a slice from an ordered collection.
- Get many elements from a collection using list of indices.
- Get many elements from a collection using a bitmap (or a boolean list).
	- A bit for each index in the collection.
- Get elements from a collection satisfying a predicate.

```fold
a[n]                -- :: Collection a ->         Key ->            a
a[n1..n2]           -- :: Collection a ->   Range Key -> Collection a
a[n1, n2, n3, n4]   -- :: Collection a ->    List Key -> Collection a
a[T, F, F, T, T]    -- :: Collection a ->   List Bool -> Collection a
a[x -> x > 3]       -- :: Collection a -> (a -> Bool) -> Collection a

a # n
a # (n1..n2)
a # [n1, n2, n3, n4]
a # [T, F, F, T, T]
a # (x -> x > 3)

a !! n
a !! (n1..n2)
a !! [n1, n2, n3, n4]
a !! [T, F, F, T, T]
a !! (x -> x > 3)

a @ n
a @ (n1..n2)
a @ [n1, n2, n3, n4]
a @ [T, F, F, T, T]
a @ (x -> x > 3)

get n a
select (n1..n2) a
select [n1, n2, n3, n4] a
select [T, F, F, T, T] a
select (x -> x > 3) a

select [T, F, F, T, T] a
```

### Using OCaml's adapted `.[ ]` syntax

```fold
a.n
a.(n1..n2)
a.[n1, n2, n3, n4]
a.[T, F, F, T, T]
a.(x -> x > 3)
```

_Note_: `a.[n]` would return a singleton list with n'th element.

```
id_list = [1, 2, 3, 4, 5]
id_list.2
id_list.(2..4)
id_list.[4, 2, 0, 1]
id_list.[T, F, F, T, T]
id_list.(\x > 3)
```


Comparison with record access syntax:

```fold
person = {`name = "Bob", `age = 34}
person.`name == person.name 
```

- There's a conflict for index and record key access. One solution is to prefix the index with a `#` that can also be used to build accessors.
- Another conflict exists when working with lists of lists: `nasted_list.0.2` is confusing not only because of strange nested notation of indexes but also because it conflicts with float indexes for maps.

```
id_list = [1, 2, 3, 4, 5]
id_list.#2
id_list.(2..4)
id_list.[4, 2, 0, 1]
id_list.[T, F, F, T, T]
id_list.(\x > 3)
```

- The regular `a[...]` syntax can be implemented if collections behave like functions.
- The field access syntax and the index access syntax are similar in nature. Both can be expressed with constants (`person.name` and `numbers[3]`) and with variables (`person[name]` and `numbers[n]` for some variables `name` and `n`). Notice thought that with traditional syntax different notation is used for constants and variables in record accesses. This suggests that an generalised solution can be defined.


## Operators

### `@` – At sign

- **Code Attributes**:

```lua
2 @ "some meta-data"

@{`doc: "hey", `author: "me"}
function sum x y
  x + y
end

type Person = {
	name : String @{field = "fullName"}
	age  : Int    @{value = Float}
} @derivng yojson
```

- **Element access**, **Element _at_**:

```
assert ([1, 2, 3, 4, 5] @ 3 == 4)
```


### `#` – Hash sign

- **Cardinality**

```
l1 = ['x', 'y', 'z']
assert (#l1 == 3)
```

- **Element access**

```
assert ([1, 2, 3, 4, 5] # 3 == 4)
```


### `&`

- **Cons**, **Add element**

```
l1 = ['a', 'b', 'c']
l2 = 'x' & l1
assert (l2 == ['x', 'a', 'b', 'c'])

x =
	case l1
	  first & rest -> first
	  [] -> undefined ()
  end
assert (x == 'x')
```

### `!`

- **`do!` notation**, **Effect handling**

```
select pred = do!
  a <- await
  if pred a then yield a
  else select pred
end

a = Some 5
b = Int.parse "7"
assert (a! + b! == Some 12)
```

- **Extract**, **Force value**

```
a = Some 3
b = Int.parse "x"
assert (a! == 3)
raises No_value b!
```


### `?`
### `.`
### `~`
### `:`
### `;`
### `$`
### `|`
### `\`
### `_`
### `^`
### `%`
### `::`
### `++`
### `->`
### `=>`
### `>>`, `<<`
### `|>`, `<|`
### `//`, `\\`