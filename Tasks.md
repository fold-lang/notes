# Tasks

## Defining Operators

Operators are simple functions with non-alphabetic names. Fold will recognizes any combination of the following symbols as an operator: -, +, *, =, ~, <, >, :, ;, |, \, /, $, !, ?, ^, %, #, @.

```
-- Infix operators

f << g = x -> f (g x)
```


```
interface Option
  _ ? _ :: Self a -> a -> a 
end

module Option
  _ ? _ :: Self a -> a -> a
  self ? default =
    case self
      | Some x -> x
      | None   -> default
    end
end

import Option(with_default, ?) in: do
  assert: None ? 'x' == 'x'
end

```

## Named Ar