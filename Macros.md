# Macros


```
syntax `if t `then a `else b =
  ```
  match $t
  | True -> $a
  | False -> $b
  end
  ```
```

List literals are also defined with macros. Consider the following grammar
definition:

```
list  = "[" "]" | items
items = expr | expr "," items
```

In Fold it can be encoded as:

```
syntax
  | `[ `] =
    `(List.empty)

  | `[ (x <- expr) (xs <- (`, expr)*) =
    `(List.prepend x xs)
```

The same grammar can be defined in the parser combinator style:

```
list = char '[' >> char ']' <|> char '[' >> items >> char ']'
items = do
  x  <- expr
  xs <- many (char ',' >> expr)
  return [x & xs]
```

```
if $cond then $e1 else $e2 =
  ```
  case $cond of:
    True  -> $e1
    False -> $e2
  ```

factorial n =
  if (n == 0) then:
    print "done"
    1
  else:
    n * factorial (n - 1)
```


## Modules

```
module Option
  type Self a = Some a | None

  def self ? default =
    match self
    | Some a -> a
    | None   -> default
    end

  include Monad.Make module
    type Self = Option.Self

    def pure = Some

    val bind f self =
      match self
      | Some a -> f a
      | None   -> None
      end
  end
end
```
