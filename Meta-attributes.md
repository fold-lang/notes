
# Fold - Meta Attributes

Some modern languages allow the programmer to annotate the code entities with
some kind of meta information.

...

Examples from Clojure, D, Python, comments with meta-data, etc.

...

In Fold, similarly to Clojure there is a _meta_ function. But unlike in Clojure
_meta_ function does not alter the original value by adding attributes. Fold
expressions are simple values, not objects, so it's impossible to alter them in
any way. Instead the original expression is wraped in a special meta expression.


```lua
-> a = 42
-> meta a
 = None
-> a = with_meta a "answer"
-> meta a
 = Some "answer"
-> a
 = 42
```

In this example we can see that asking for meta data of the newly created values gives us `None`.
If we add a meta attribute to the same value we get back a wrapped value. Now if we try to call _meta_ again, it will give us back "answer" as expected. The interessting property of meta attributes is that they don't change the way expressions are evaluated, which means that a wraped meta value behaves exactly the same way as the original one.

Meta attributes exist only on the syntax level and are used by macros for compile-time processing.

Use cases:

- documentation generation, source documentation is stored as meta attributes.
- version management, compatibility between functions, etc.
- performance hints, inline, tco
- TODO: see the use cases of attributes in D.
- May be used for some sort of type validadion: Consider the case where you want to restrict the values of some type. Let's say the int `x` has to be positive in the following example:

  type Positive a = a > 0
  x = read stdin :: Positive Int


...

One alternative would be to create a separate expression entity called `Meta of
expr * expr` but using the existing LISP-based syntax tree gives more flexibility.


