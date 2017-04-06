https://groups.google.com/forum/#!topic/fa.caml/c4q1A8GMzAY

Dear all,
Intro
=====

Inspired by Simon Peyton Jones' proposal of simple views for Haskell 
[1,2], I incorporated something very close to his proposal into Micmatch 
[3]. It's an experimental feature of Micmatch 0.697 [4,5] which is 
available in GODI.


What are views?
===============

Views allow pattern matching on data that don't have a type that allows 
pattern matching. Useful uses of views in OCaml include the following:

* pattern matching over object fields (methods w/o arguments)
* pattern matching over lazy values, such as lazy lists
* filtering sets of elements (e.g. Even/Odd, Positive, Short_list, ...)
* generally: matching abstract values when you only have functions to
   obtain information about their structure.
* parametrization of pattern matching (patterns with gaps filled at runtime)

In general the use of views avoids breaking match-with blocks into several 
pieces when the patterns are complex.

There are several kinds of views, each with their own advantages and 
difficulties. The main disadvantage of the solution that we chose here is 
that it prevents the detection of redundant or missing cases whenever a 
view pattern is used. The advantages are the flexibility and simplicity of 
use: several overlapping views can be used in the same match-with 
block (e.g. match x with (%Even | %Positive) -> ... | ... -> ...).


Example
=======

(* Define a view function view_XY *)

let view XY = fun obj -> try Some (obj#x, obj#y) with _ -> None


(* Test if a list of objects starts with coordinates x=0 and y=0 *)

let starts_from_origin = function
     %XY (0, 0) :: _ -> true
   | _ -> false


You can see that a view is simply defined as a function that takes the 
subject value and returns an option. This is the case of views with 
an argument. Views without arguments must be defined as predicates (return 
a bool).


Efficiency
==========

Don't use simple views such as %Even or %Positive if you are concerned 
about speed at this particular point in your program.


References
==========

[1] SPJ's email: http://www.haskell.org/pipermail/haskell/2007-January/019014.html
[2] Proposal for Haskell: http://hackage.haskell.org/trac/ghc/wiki/ViewPatterns
[3] Micmatch home: http://martin.jambon.free.fr/micmatch.html
[4] Views in Micmatch: http://martin.jambon.free.fr/micmatch-manual.html#htoc10
[5] Wiki for your comments: http://ocaml.pbwiki.com/Micmatch

Martin

--
Martin Jambon
http://martin.jambon.free.fr

