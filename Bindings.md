# Definitions


## Module Scope

```
-- Values
def x = 42          3
val x = 42          2
let x = 42          1

-- Functions
def f x = x         3
fun f x = x         4
function f x = x    2
let f x = x         1

-- Variables
var x = 42          1
val x = ref 42      2
def x = ref 42      3

-- Modules
module List ... end

-- Module Functions
module List (X::S) ... end

-- Interfaces
interface List ... end

-- Interface Functions
interface List (X::S) ... end
```

**Choice**:

```
def x = 42
def f x = x

let x = 42 in ...
let f x  = x in ...
```

Using separate keywords for values and functions is not consistent with the usage of `let`.


## Expression Scope

```
-- Values
val x = 42 in
let x = 42 in
val x = 42;
let x = 42;

-- Functions
fun f x = x in
let f x = x in
fun f x = x;
let f x = x;

-- Variables
var x = 42 in
var x = 42;

-- Modules
module List ... end

-- Module Functions
module List (X::S) ... end

-- Interfaces
interface List ... end

-- Interface Functions
interface List (X::S) ... end
```


## Alternatives

**Global Scope**

- `let/let`
- `def/def`
- `val/fun`
- `val/def`
- `val/function`
- `value/function`
- `let/function`


```
-- Let for everything
(global let)     + (local let + in)
(global let)     + (local let + ;)

-- Def for global and let for local
(global def)     + (local let + in)
(global def)     + (local let + ;)

-- Val/fun for global and local
(global val/fun) + (local val/fun + in)
(global val/fun) + (local val/fun + ;)

-- Val/fun for global and let for local
(global val/fun) + (local let + in)
(global val/fun) + (local let + ;)
```


