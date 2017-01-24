# Functions

- All functions in Fold are curried.
- All functions have a known type.
- Functions can have named arguments.
- Functions are simple lambdas bound to names.

## Run-time Functions

```
factorial :: Int -> Int = n ->
  if n == 0
    then 1
    else n * factorial (n - 1)
```

**Named arguments**

```
print (to out :: File = stdin) (end = "\n") str =


print to: stderr "hello"
(print (: to stderr) "hello")

print to: (open "/tmp/x.txt") "hello"
```


## Compile-time Functions

- Can be used as language macros.
- Take code (_i.e._ quoted expressions) as an argument and produce code as the result.

```
time :: Expr -> Expr = body ->
  quote
    t = Time.now ()
    r = $body
    print "Elapsed time: $(t - Time.now ()) seconds"
    r
  end

time `(factorial 100)

-- * --

if t (then a) (else b) =
  ...


-- Examples

factorial n =
  if (n == 0)
    then: 1
    else: (n * factorial (n - 1))

factorial n =
  if (n == 0) then: {
	  print "done"
	  1
	} else: {
	  n * factorial (n - 1)
	}

factorial n =
  if (n == 0) then
	  print "done"
	  1
	else
	  n * factorial (n - 1)
	end
```


## Function Definition

### Case Functions

Case functions provide a syntax for pattern matching of arguments in the function definitions.

```
def factorial 0 = 1
  | factorial n = n * factorial(n - 1)
```

**Benefits**:

- Simplicity: simple semantics.
- Clarity: easy to read and understand.
- Experience: straightforward to add new cases.


**Drawbacks**:

- Comments for the first pattern conflict with the function comments.

```
-- Base case?
def factorial 0 = 1
-- Recursion
  | factorial n = n * factorial(n - 1)
```

- Indentation without new level.

```
def factorial 0 = 1
  | factorial n =
    n * factorial(n - 1)
```


### Arrows in Function Definition

The usage of the `=>` (see examples below) is an interesting syntactic element used to define functions.

```
-- Creates a unit function
def say_hello =>
  print "hello"

-- Equivalent to:
def say_hello =
  () => prtin "hello"
  
def () = say_hello ()

-- Simple function definition.
def hello name =>
  print "Hello, $name!"

-- Function with pattern matching.
def factorial
  | 0 => 1
  | n => n * factorial (n - 1)
end
```

**Benefits**:

- Visual distinction between value and function definitions.
- Consistent syntax for simple and case functions (_i.e._ `=` vs `=>` for function body).
- Easy to create a named function of anonymous function (_i.e._ no need to replace `=>` by `=`).
- Visually aligns with the `::` operator as opposed to `=`.

**Drawbacks**
 
- Noisier syntax. The simple `=` is an invisible token (akin to `,` and `.`) while `=>` attracts attention.