-- Syntax Experiments



-> x y -> x + y
-> {~ + ~}
-> (~ + ~)


- Macro monads.

(create-visit \x)


say_hello :: Str? -> Unit
say_hello (name = "World") = do
    default_msg_fmt = "Hello, %s"
    print default_msg_fmt % name
end


say_hello :: Str? -> Unit
say_hello (name = "World") = {
    default_msg_fmt = "Hello, %s"
    print default_msg_fmt % name
}


say_hello :: Str? -> Unit
say_hello (name = "World") =
    default_msg_fmt = "Hello, %s"
    print default_msg_fmt % name


-- // --

say_hello (name :: Str = "World") -> Unit = do
    Log.info "Will say hello"
    default_msg_fmt = "Hello, %s"
    print default_msg_fmt % name
end


say_hello (name :: Str = "World") -> Unit = {
    Log.info "Will say hello"
    default_msg_fmt = "Hello, %s"
    print default_msg_fmt % name
}

-- // --

module Graph (Item :: Hashable) {

}

module Graph (Item :: Hashable) do

end


-- // --

sum x y = do
    x + y
end

sum = x y -> x + y

function sum x y
    x + y
end

-- Explicit function definition.
function (sum x y)
    x + y
end


-- Function definition with types.
function sum (x::Int) (y::Int) -> Int
    x + y
end

-- Explicit function definition with types.
function (sum (x::Int) (y::Int) -> Int
    x + y
end

-- Explicit function definition with types.
function (sum (x::Int) (y::Int) -> Int {
    x + y
}


x ⊳ f

[x^2 : x <- [1..99]]
  ⊳ sort by: normalize
  ⊳ x -> Float x
  ⊳ print
  
  
  
sum = x y -> x + y

sum : Int -> Int -> Int
sum = function x y ->
    x + y
end

range :: Int -> Int -> Int
range <- (from start = 0) (to stop) (by step = 1) ->
    if | start == stop -> []
       | else -> [start & range from: start + step
                                  to: stop
                                  by: step]

range :: Int -> Int -> Int
range => (from start = 0) (to stop) (by step = 1) ->
    if | start == stop -> []
       | else -> [start & range from: start + step
                                  to: stop
                                  by: step]

range => (from start = 0) (to stop) (by step = 1) ->
    if | start == stop -> []
       | else -> [start & range from: start + step
                                  to: stop
                                  by: step]



-- // --

The type annotation expression:

    x :: a

- `::` is a type restriction macro - adds the annotation to the code model representation.
- Type restriction is not the same as type conversion.
- Type construction may require multiple arguments, while type restriction operates on single value.



-- # Lambda Syntax

x y => x + y

function x y =>
    x + y
end


function sum: x y =>
    x + y
end

map [1..99] do x =>
    x * 2
end

map [1..99] x => do
    x * 2
end




range :: Int -> Int -> Int
range start stop step =
    if | start == stop -> []
       | else -> [start & range from: start + step
                                  to: stop
                                  by: step]

