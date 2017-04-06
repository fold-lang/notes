

## Fold


syntax if =
  | ->



## Nim

### Infix Operators

```
proc `^`*[T: SomeInteger](base: T, exp: T): T =
  var (base, exp) = (base, exp)
  result = 1

  while exp != 0:
    if (exp and 1) != 0:
      result *= base
    exp = exp shr 1
    base *= base

echo 2 ^ 10 # 1024
```


### Term rewriting

```
template optLog1{a and a}(a): auto = a
template optLog2{a and (b or (not b))}(a,b): auto = a
template optLog3{a and not a}(a: int): auto = 0

var
  x = 12
  s = x and x
  # Hint: optLog1(x) --> ’x’ [Pattern]

  r = (x and x) and ((s or s) or (not (s or s)))
  # Hint: optLog2(x and x, s or s) --> ’x and x’ [Pattern]
  # Hint: optLog1(x) --> ’x’ [Pattern]

  q = (s and not x) and not (s and not x)
  # Hint: optLog3(s and not x) --> ’0’ [Pattern]
```

## Shen

```
(define make-list
  [A|D] -> [cons (make-list A) (make-list D)]
  X -> X)

(defmacro info-macro
  [info Exp] -> [output "~A: ~A~%" (make-list Exp) Exp])

(info (* 5 6)) \\ outputs [* 5 6]: 30


=> (defmacro +-macro
       [A + B] -> [+ A B])
macro
+-macro

=> (1 + (* 2 3))
7


(defmacro branch-if-macro
  [branch-if Cond1 Cond2 Both Fst Snd None] ->
    [if Cond1
        [if Cond2 Both Fst]
        [if Cond2 Snd None]])
```

## Clojure

```
(defmacro if2 [[cond1 cond2] bothTrue firstTrue secondTrue else]
  `(let [cond1# ~cond1
         cond2# ~cond2]
     (if cond1# (if cond2# ~bothTrue   ~firstTrue)
                (if cond2# ~secondTrue ~else))))
```

## Scheme

```
(define-syntax if2
  (syntax-rules ()
    ((if2 cond1 cond2 both-true first-true second-true none-true)
     (let ((c2 cond2))
       (if cond1
           (if c2 both-true first-true)
           (if c2 second-true none-true))))))
```


## Idris

```
syntax if [test] then [t] else [e] = ifThenElse test t e;

term syntax "[" [o] [s1] ":" [arg1] [s2] ":" [arg2] "]" = (return o) >>= (s1 arg1 {s2 = arg2})


greeting <- [greeting stringByReplacingOccurrencesOfString: recipient withString: replacement]

```

## Rust

```
let x: Vec<u32> = vec![1, 2, 3];

macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}

let x: Vec<u32> = {
    let mut temp_vec = Vec::new();
    temp_vec.push(1);
    temp_vec.push(2);
    temp_vec.push(3);
    temp_vec
};

```

## Dylan

```
define macro table
  { table(?table-class:expression, ?table-contents) }
    => { let ht = make(?table-class); ?table-contents; ht; }
  { table(?rest:*) } => { table(<table>, ?rest); }

  table-contents:
  { } => { }
  { ?key:expression => ?value:expression, ... }
    => { ht[?key] := ?value; ... }
end macro table

```


## Rascal

```
module demo::lang::Exp::Concrete::WithLayout::Syntax

layout Whitespace = [\t-\n\r\ ]*;

lexical IntegerLiteral = [0-9]+;

start syntax Exp
  = IntegerLiteral
  | bracket "(" Exp ")"
  > left Exp "*" Exp
  > left Exp "+" Exp
  ;
```
