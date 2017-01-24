# Macros

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

  include Monad.Make(module
    type Self = Option.Self

    def pure = Some

    bind f self =
      match self
      | Some a -> f a
      | None   -> None
      end
  end)
end
```