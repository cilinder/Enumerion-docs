# Subtyping

Enumerion supports [subtyping](https://en.wikipedia.org/wiki/Subtyping#).
It uses a form of [structural subtyping](https://en.wikipedia.org/wiki/Structural_type_system).

A simple examples is that `Fin n` is a subtype of `Nat` for any `n` which makes `Fin n` well typed.

This means we can use a value of type `Fin n` anywhere that a value of type `Nat` is expected.
This is called the [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). For examples, if we have a function

```enum
# def f := fun (x : Nat) => (x + 1) ** 2
f is defined.
```

we can apply it to any value of type `Fin n`

```enum
# def x := (3 : Fin 10)
x is defined.
# eval f x
16
     : ℕ
```

Furthermore `Fin n` is a subtype of `Fin m` as soon as `n <= m`.
This however requires requires a proof. In general at the typechecking phase, we may not be able to
provide such a proof, since `n` and `m` may be arbitrary terms of type `Nat`.
For instance if we defined `m := n * n`, we would have to provide a proof of
`∀ (n : ℕ), n <= n * n`, which requires the induction principle on `ℕ`.

Currently, we don't attempt to prove such propositions and simply assume it holds,
and issue the user a warning that we made such an assumption.

```enum
# def square := fun (n : Nat) => n * n
square is defined.
# check 3 : Fin (square 3)
Warning: assuming 4 ≤ square 3

3
     : Fin (square 3)
```

In the future, we may try to prove simple cases of such propositions,
or offload it to an [SMT](https://en.wikipedia.org/wiki/Satisfiability_modulo_theories) solver.


## Rules for subtyping
