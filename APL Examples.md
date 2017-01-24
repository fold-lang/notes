- _Compression_: use a bit vector to select out elements of another vector.
- <http://stackoverflow.com/questions/22742281/how-do-i-do-in-f-what-would-be-called-compression-in-apl?rq=1>

```apl
a ← ι10
b ← (~a ∊ 4 6) / a
```

```fold
a = ^10
b = select (not (a in? [4, 6])) a
b = not (a in? [4, 6]) // a
b = a \\ not (a in? [4, 6])
b = a # not (a in? [4, 6])
```

---

```
+ / ints 10
(ints 5) + (rev ints 5))
5 rho 6
```

```
+ / ^10

^10 / +

reduce (+) ^10

ints 5 + rev (ints 5)

-> 5 $ 6
[6, 6, 6, 6, 6]
```


```
op | Monadic            | Diadic
 ⍴ | Returns dimensions | Creates vector or array
```

```
a = [1, 2, 3;
     4, 5, 6]
-> $a
[3, 2]
```
