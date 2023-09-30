# Hello World

Once we have Enumerion installed let's try to enumerate something.

First run enumerion.

```
./enumerion.exe
```

This should give us the basic REPL.

```Enumerion
Enumerion
# 
```

You can exit Enumerion with `Ctrl + D`.

We have the natural numbers and numbers from `0` to `n-1`.

```Enumerion
def X := structure { x : 4 = 3; y : Fin 4 }
def V := variant { `foo ℕ | `boo Fin 5 }
check Nat
check True && False
def Y := product (x : Fin 4), Fin (x + 1 * 3 - 2)
```

```Enumerion
# check Nat
ℕ
     : Enum
# check Fin 3
Fin 5
     : Finite
```

We see that Enumerion keeps track of the "enumerability" of these types, and it says `ℕ` is in the `Enum` universe and `Fin 5` is in the `Finite` universe.

We can ask Enumerion to enumerate `Fin 5`.

```enum
# enumerate Fin 5
0
1
2
3
4
Size is 5.
```

It prints all the numbers from 0 to 4 and tells you the size of the structure it enumerated (in this case 5).

### More elaborate example

Let's enumerate all the monotone functions from `Fin 5` to `Fin 2`.

First define the structure we are going to enumerate.

```enum
# def monotone := structure { f : Fin 5 -> Fin 2; axiom monotone : forall (x y : Fin 5), x <= y -> f x <= f y }
monotone is defined.
```

Now enumerate it.

```enum
# enumerate monotone

{f := {2 ↦ 0, 4 ↦ 0, 0 ↦ 0, 1 ↦ 0, 3 ↦ 0}}
{f := {2 ↦ 0, 4 ↦ 1, 0 ↦ 0, 1 ↦ 0, 3 ↦ 0}}
{f := {2 ↦ 0, 4 ↦ 1, 0 ↦ 0, 1 ↦ 0, 3 ↦ 1}}
{f := {2 ↦ 1, 4 ↦ 1, 0 ↦ 0, 1 ↦ 0, 3 ↦ 1}}
{f := {2 ↦ 1, 4 ↦ 1, 0 ↦ 0, 1 ↦ 1, 3 ↦ 1}}
{f := {2 ↦ 1, 4 ↦ 1, 0 ↦ 1, 1 ↦ 1, 3 ↦ 1}}
Size is 6.
```

We see that in the final result, the axioms are not displayed, since they are just computationally checked by Enumix and the witness is not stored.

Let's tabulate the data so we can more clearly see what Enumerion produced.

| x   | 0 | 1 | 2 | 3 | 4 |
|-----|---|---|---|---|---|
| **f x** | 0 | 0 | 0 | 0 | 0 |
|     | 0 | 0 | 0 | 0 | 1 |
|     | 0 | 0 | 0 | 1 | 1 |
|     | 0 | 0 | 1 | 1 | 1 |
|     | 0 | 1 | 1 | 1 | 1 |
|     | 1 | 1 | 1 | 1 | 1 |


