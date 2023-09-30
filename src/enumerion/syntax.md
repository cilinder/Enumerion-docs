# Syntax

<!-- toc -->

Here we describe the syntax of Enumerion

# Expressions

Enumerion has types and terms. A special subset of the terms are the [propositions](#propositions).

## Types

It has some builtin types:
- `Nat` or `ℕ` the type of natural numbers
- `Fin n` where `n` is a natural number is the set `{0,…,n-1}`
- `Prop` the universe of decidable propositions.
- `Finite` the universe of finite types.
- `Enum` the universe of enumerable types.

It also has ways to build new types out of old ones:
- `∏ (x : A), B(x)` or `product (x : A), B(x)` is a dependent product (or Pi-type).
- A special case is where `B(x)` does not actually depend on `x`. In this case it is just the
  usual function space `A → B`.
- `structure { x₁ : A₁; x₂ : A₂(x₁); … ; xₙ : Aₙ(x₁,…,xₙ₋₁) }` is a multi-field version of a Sigma-type.
  A special case is where each `Aᵢ` doesn't depend on any of the previous `xⱼ`. In this case
  we have the product `A₁ × A₂ × … × Aₙ`.
- ``variant { `t₁ : A₁ | … | `tₙ : Aₙ }`` variants (which are like tagged [union types](https://en.wikipedia.org/wiki/Union_type#)).

## Terms

Each type has one or more constructors for defining terms (elements) of that particular type:

### Numbers

Elements of `Nat` are numerals. We can just type them in:

```enum
# check (4 : Nat)
4
     : ℕ
```

In fact if we just type in a numeral, the typechecker looks for the "smallest" `Fin n` it is
an element of and assigns it to this type:

```enum
# check 4
4
     : Fin 5
```

This doesn't cause problems because of [subtyping](/enumerion/subtyping.md).

There are some builtin operations we can apply to numbers:
- `n + m`
- `n - m`
- `n * m`
- `n ** m` (exponentiation, python style)
- `n / m` whole number division

### Functions

We define function with either the `fun` keyword or with a `λ`.
The syntax is `fun (x : A) => expr(x)`, e.g.

```enum
# check fun (x : Nat) => x ** 2
λ (x0 : ℕ), x0 ** 2
     : ℕ → ℕ
```

Functions can also return types

```enum
# check λ (x : Nat) => Fin x
λ (x0 : ℕ), Fin x0
     : ℕ → Finite
```

We apply functions as usual in functional programming (in curried form)

```enum
# def f := fun (x y : Nat) => x + y + 1
f is defined.
# eval f 1 2
4
     : ℕ
# check f 1
f 1
     : ℕ → ℕ
```

### Structures and Records

Structures are slight generalizations of [Sigma types](https://en.wikipedia.org/wiki/Dependent_type#%CE%A3_type) that, instead of having two fields, have finitely many fields and later fields
may depend on all the previous ones. Each field is also denoted with a label, so we can reference
it. 

An element of a structure is called a **record**.

Let's define a structure and constructs some of its elements.
We define a new structure using the `structure` keyword, then list the fields between curly braces,
separated by semi-colons.
We denote each field of the structure as a pair `<lbl> : <type>`, where `<lbl>` is the name of the
field and `<type>` its type.

```enum
# def S := structure { x : Nat; y : Fin x }
S is defined.
```

Notice that the type of the field `y` depends on the *value* of `x`.

Now define a record of type `S`.

```enum
# def s := { x := 5; y := 3 } : S
s is defined.
# check s
s
     : S
```

We define records between curly braces, again as a semi-colon separated list of pairs of
the form `<lbl> := <expr>`. Notice that above `y` is indeed of type `Fin 5`.
If we chose a different value for `y` the expression might not typecheck

```enum
# check { x := 5; y := 5 } : S
Typechecking error at line 1, characters 21-22:
this expression should have type Fin 5 but has type Fin 6
```

We can project out of records

```enum
# eval s.x
5
     : ℕ
```

### Variants and Tags

Variants in Enumerion are like a disjoint union of its possible (tagged) types.
A variants looks like this

```enum
# def V := variant { `foo : Nat | `moo }
```

as seen above, a tag may or may not have an associated type.
Elements of a variant are called **tags** and we can build them as such

```enum
# check `foo 3 : V
`foo 3
     : V
```

We can use variants with `match` statements. Match has the syntax

```enum
match <tag> with
| `tag₁ (x₁ : A₁) => e₁
⋮
| `tagₙ (xₙ : Aₙ) => eₙ
```

where `<tag>` is a tag of some variant `V` and `` `tag₁,…,`tagₙ `` are all the tags of `V` and
`e₁,…,eₙ` have the same type*.

Using variants and `match` statements, we can encode an if-then-else function:

```enum
# def B := variant { `true | `false }
B is defined.
# def ite := fun (A : Finite) (b : B) (x y : A) => match b with | `true => x | `false => y end
ite is defined.
# check ite
ite
     : Π (A : Finite), B → _x0 → _x0 → A
```

## Propositions

