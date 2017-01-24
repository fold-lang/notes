# Examples

## Elm – Function Definition with Case

```elm
evaluate : Boolean -> Bool
evaluate boolean =
  case boolean of
    Literal bool ->
        bool

    Not b ->
        not (evaluate b)

    And b b' ->
        evaluate b && evaluate b'

    Or b b' ->
        evaluate b || evaluate b'
```


```fold
evaluate :: Boolean -> Bool
evaluate value =
  case value of:
    Literal a ->
        a

    Not a ->
        not (evaluate a)

    And a b ->
        evaluate a && evaluate b

    Or a b ->
        evaluate a || evaluate b
```


## Elm – Function Definition with Pipe

```elm
maybeToList : Maybe a -> List a
maybeToList maybe =
  maybe
    |> Maybe.map (List.repeat 1)
    |> Maybe.withDefault []
```

```fold
maybe_to_list : Maybe a -> List a
maybe_to_list maybe =
  maybe
    |> Maybe.map (List.repeat 1)
    |> Maybe.with_default []
```

## Lambda Functions

```
xs
|> map parse_item
|> filter do
     | Some x -> x % 5 == 0
     | None -> false
   end
|> take
|> fold (+) 0
     
do Some x -> x
 | None -> 0
end


fun | pat => e
    | pat2 => e
....|


```