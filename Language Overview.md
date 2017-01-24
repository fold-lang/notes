
# Language Overview

Overview of the language elements of Fold.

- [1 Introduction](#introduction)
- [2 Comments](#comments)
- [3 Literals](#literals)
    - [3.1 Booleans](#booleans)
    - [3.2 Numbers](#numbers)
    - [3.3 Text](#text)
    - [3.4 Symbols](#symbols)
- [4 Expressions](#expressions)
    - [4.1 Arithmetic Operations](#arithmetic-operations)
    - [4.2 Blocks](#blocks)
    - [4.3 Function Application](#function-application)
- [5 Control Flow](#control-flow)
    - [5.1 Conditionals](#conditionals)
        - [5.2 If Expressions](#if-expressions)
        - [5.3 When Expressions](#when-expressions)
    - [5.2 Pattern-matching](#pattern-matching)
        - [5.2.1 Match Expressions](#match-expressions)
        - [5.2.2 Conditional Matching](#conditional-matching)
        - [5.2.3 Pattern Views](#pattern-views)
    - [5.3 Exceptions](#exceptions)
    - [5.4 Loops](#loops)
        - [5.4.1 While Loops](#while-loops)
        - [5.4.2 For Loops](#for-loops)
        - [5.4.3 Recursion](#recursion)
- [6 Bindings](#bindings)
    - [6.1 Definitions](#definitions)
    - [6.2 Variables](#variables)
    - [6.3 Let Expressions](#let-expressions)
    - [6.4 Where Expressions](#where-expressions)
- [7 Types](#types)
    - [7.1 Primitive Types](#primitive-types)
    - [7.2 Tuples](#tuples)
    - [7.3 Records](#records)
    - [7.4 Anonymous Records](#anonymous-records)
    - [7.5 Parametric Types](#parametric-types)
    - [7.6 Union Types](#union-types)
    - [7.7 Abstract Data Types](#abstract-data-types)
- [8 Collections](#collections)
    - [8.1 Lists](#lists)
    - [8.2 Sets](#sets)
    - [8.3 Arrays](#arrays)
    - [8.4 Dicts](#dicts)
- [9 Functions](#functions)
    - [9.1 Anonymous Functions](#anonymous-functions)
    - [9.2 Function Definitions](#function-definitions)
    - [9.3 Named Arguments](#named-arguments)
    - [9.4 Custom Operators](#custom-operators)
- [10 Modules](#modules)
    - [10.1 Module Definitions](#module-definitions)
    - [10.2 Signatures](#signatures)
    - [10.3 Type Modules](#type-modules)
    - [10.4 Module Extensions](#module-extensions)
    - [10.5 Module Functions](#module-functions)
    - [10.6 Imports and Includes](#imports-and-includes)
- [11 Error Handling](#error-handling)
- [12 Metaprogramming](#metaprogramming)
    - [12.1 Meta Attributes](#meta-attributes)
    - [12.2 Static Introspection](#static-introspection)
    - [12.3 Macros](#macros)


# 1 Introduction


# 2 Comments

-- This is a single-line comment.

{{ This is a multi-line comment.

   {{ Multi-line comments can be nested. }}
}}


# 3 Literals

```fold
-- Booleans
True False

-- Integers
42 1_000_000
Int.min Int.max

-- Floats
3.14 0.999
nan infinity

-- Characters
'x' '\n' '0xFF'

-- Strings
"Hello, World!"

"""
This is a multi-line string.
Multi-line strings can contain "quotation marks".
"""

-- Symbols
`hello `foo `x
```


# 4 Expressions

## 4.1 Basic Operations

```fold
-- Boolean operators are [&&], [||] and [not].
True && not (True || False)

(2 + 4) * (4 ** 2 - 9)

"hello" ++ " " ++ "world" ++ "!"
```


# 4.2 Bindings

```fold
-- Definitions

-- Variables

-- Let Expressions

-- Where Expressions

```


## 4.3 Blocks




## 4.4 Function Application

```
print ("The squre root of 81 is %d" % Math.sqrt 81)
```


# 6 Control Flow
## 6.1 Conditionals
## 6.2 If Expressions

```fold
x = 10
print (if (x == 10) "yeah" else: "nope")
```

## 6.3 When Expressions
## 6.2 Pattern-matching
### 6.2.1 Match Expressions
### 6.2.2 Conditional Matching
### 6.2.3 Pattern Views
## 6.3 Exceptions
## 6.4 Loops
### 6.4.1 While Loops
### 6.4.2 For Loops
### 6.4.3 Recursion


# 7 Types
## 7.1 Primitive Types
## 7.2 Tuples
## 7.3 Records
## 7.4 Anonymous Records
## 7.5 Parametric Types
## 7.6 Union Types
## 7.7 Abstract Data Types


# 8 Collections
## 8.1 Lists
## 8.2 Sets
## 8.3 Arrays
## 8.4 Dicts


# 9 Functions
## 9.1 Anonymous Functions
## 9.2 Function Definitions

```fold
def sum_of_squares (x::Int) (y::Int) -> Int
  let
    x2 = x * x
    y2 = y * y
  in
    x2 + y2
end

def sum_of_squares x y = x * x + y * y

-> sum_of_squares 3 4
:: Int = 25
```

## 9.3 Named Arguments
## 9.4 Custom Operators


# 10 Modules
## 10.1 Module Definitions
## 10.2 Signatures
## 10.3 Type Modules
## 10.4 Module Extensions
## 10.5 Module Functions
## 10.6 Imports and Includes


# 11 Error Handling


# 12 Metaprogramming
## 12.1 Meta Attributes
## 12.2 Static Introspection
## 12.3 Macros


