# Demo

Once you have Enumerion [installed](/installation.md) you can run the executable via `./enumerion.exe`.
This should give you a REPL prompt

You can download the current version (2023-30-09) from [this link](https://unilj-my.sharepoint.com/:u:/g/personal/jure_taslak_fmf_uni-lj_si/EYJThEbNm0NLl_M8dEzb08UB7K7dLgGR1Aqs406Szhnl1g?e=D9gVMf).

```enum
Enumerion
#
```

where you can input [toplevel commands](/enumerion/toplevel.md). Here we'll be focusing mostly on `enumerate`.
This enumerates all the elements of a given type.

It is used as follows:

```enum
# enumerate <expr>
```

where `<expr>` is an enumerion expression. The syntax of Enumerion is described in the
[syntax](/enumerion/syntax.md) section.

# Examples

## Functions

Let's first try to enumerate some simple things then more to some more interesting examples.

```enum
def A := Fin 3 -> Fin 2
enumerate A
```

If we run this program we get the output

```
{2 ↦ 0, 0 ↦ 0, 1 ↦ 0}
{2 ↦ 1, 0 ↦ 0, 1 ↦ 0}
{2 ↦ 0, 0 ↦ 1, 1 ↦ 0}
{2 ↦ 0, 0 ↦ 0, 1 ↦ 1}
{2 ↦ 1, 0 ↦ 1, 1 ↦ 0}
{2 ↦ 1, 0 ↦ 0, 1 ↦ 1}
{2 ↦ 0, 0 ↦ 1, 1 ↦ 1}
{2 ↦ 1, 0 ↦ 1, 1 ↦ 1}
Size is 8.
```

There are 8 functions from `Fin 3` to `Fin 2`. We wouldn't need to list all of them to know this.
We can also just ask Enumerion to compute the size of the type `A`.

```enum
# eval (size A)
8
     : ℕ
```

In cases such as this, Enumerion knows it doesn't have to enumerate all elements to compute the size
of the type.

## Structures

An important construction we have available in Enumerion are [structures](/enumerion/syntax.md#structures-and-records).

We can use them to build types with multiple fields which can depend on each other.
For example

```enum
def S := structure { x : Fin 5; y : Fin x; z : Fin y }
```

Now it becomes a bit trickier to compute the number of element of type `S` by hand.
We can ask Enumerion to list them

```enum
# enumerate S
{x := 2; y := 1; z := 0}
{x := 3; y := 1; z := 0}
{x := 3; y := 2; z := 0}
{x := 4; y := 1; z := 0}
{x := 3; y := 2; z := 1}
{x := 4; y := 2; z := 0}
{x := 4; y := 2; z := 1}
{x := 4; y := 3; z := 0}
{x := 4; y := 3; z := 1}
{x := 4; y := 3; z := 2}
Size is 10.
```

## Prime numbers

A prime number can be described as a natural number with the only divisors itself and 1.
We can describe this property of numbers as a function in Enumerion.

```enum
def is_prime := fun (n : ℕ) => n > 1 ∧ ¬∃ (k l : Fin (n + 1)), 
	1 < k ∧ 1 < l ∧ k * l = n
check is_prime
```

We can now check if some numbers are prime.

```enum
# eval is_prime 2
⊤
     : Prop
# eval is_prime 4
⊥
     : Prop
```

Let's define a structure containing all the prime numbers and enumerate it.

```enum
def primes := structure {
	n : ℕ;
	axiom prime : is_prime n;
}
enumerate primes
```

we get an endless output which looks something like:

```
{n := 2}
{n := 3}
{n := 5}
{n := 7}
{n := 11}
{n := 13}
...
```

## Monoids

A lot of structures mathematicians are interested in have more structure than just tuples of
numbers.  To describe this structure, the sets and functions have to satisfy some properties,
described as propositions on those sets.

For example, a monoid is typically described as a set `X` together with an associative binary
operation `* : X × X → X` such that there exists a special element `e ∈ X`, called the **unit**
for `*`, such that for every `x ∈ X` we have `x * e = x` and `e * x = x`.

We can describe monoids in Enumerion as structures like so:

```enum
def Mon := structure {
	X : Finite;
	mul : X → X → X;
	axiom assoc : ∀ (x y z : X), mul x (mul y z) = mul (mul x y) z;
	e : X;
	axiom unit : ∀ (x : X), mul e x = x ∧ mul x e = x;
}
```

Let's go through this definition.

- The first line `X : Finite` says `X` is the carrier set of the monoid. Since enumerion is designed
to enumerate finite structures, here we say we want it to be a finite set.

- The second like, defines a function `mul` taking two parameters from `X` and returning another
element in `X`. This is the [curried](https://en.wikipedia.org/wiki/Currying) form of a
two-parameter function. Notice that the type of `mul` depends on the particular `X`.

- The third line starts with the keyword `axiom`. This signifies that this is a condition the structure needs to satisfy. Annotating this line allows the enumeration engine Enumix to perform some optimizations when generating examples of this structure. It says that for any `x, y, z ∈ X`, we have
`x * (y * z) = (x * y) * z`.

- The fourth line says we want to designate a special element `e`.

- The last like describes the property of the unit.

## Latin square

A [Latin square](https://en.wikipedia.org/wiki/Latin_square) is an `n × n` grid filled with
`n` different symbols, such that in each row and column each symbol appears exactly once.

An Latin square of size 3 looks like

|   |   |   |
|---|---|---|
| A | B | C |
| C | A | B |
| B | C | A |

Let's generate some Latin squares in Enumerion.
We'll use numbers `{0,…,n-1}` as the symbols.

First define a few auxiliary functions

```enum
def is_injective_fin := fun (A : Finite) (f : A -> A) =>
	∀ (x y : A), (f x = f y => x = y)

def is_bijective_fin := is_injective_fin

def transpose := fun (A : Finite) (f : A -> A -> A) => (fun (i j : A) => f j i)
```

We now describe a Latin square as a function `Fin n → Fin n → Fin n` (aka a `n × n` matrix
with values in `Fin n`) such that each row and column of the matrix are bijections.

```enum
def n := 3
def latin_square := structure {
    s : Fin n -> Fin n -> Fin n;
    axiom row_bij : ∀ (i : Fin n), is_bijective_fin (Fin n) (s i);
    axiom col_bij : ∀ (i : Fin n), is_bijective_fin (Fin n) ((transpose (Fin n) s) i);
}

enumerate latin_square
```

We get a result similar to

```
...
{s :=
{2 ↦ {2 ↦ 2, 0 ↦ 0, 1 ↦ 1}, 0 ↦ {2 ↦ 1, 0 ↦ 2, 1 ↦ 0}, 1 ↦
{2 ↦ 0, 0 ↦ 1, 1 ↦ 2}}}
Size is 12.
```

if we put the result in a table, we see that we got a Latin square.

|   |   |   |
|---|---|---|
| 2 | 0 | 1 |
| 1 | 2 | 0 |
| 0 | 1 | 2 |

## Categories

A [category](https://en.wikipedia.org/wiki/Category_theory) can be described as a set
`Ob` of *objects* of the category and a set `Hom(A,B)` for each two objects `A, B ∈ Ob`[^note],
called the set of *morphisms* (or *arrows*) from `A` to `B`.
We often denote `f ∈ Hom(A,B)` as `f : A → B`.

These sets must satisfy the following properties:
- If we have two morphisms `f : A → B`, `g : B → Z`, then there exists a morphism
  `h : A → Z` called the *composition* of `f` and `g` and usually denoted as `g ∘ f`.
- Composition is an associative operation.
- For each object `A ∈ Ob` there exists a morphism `1 : A → A`, such that for any `f : A → B`
  we have `f ∘ 1 = f` and `1 ∘ f = f`.

We can encode this as a structure in Enumerion. We encode the sets of morphisms
as a dependent function which takes two objects and returns a finite set.

```enum
def Category := structure {
	Ob : Finite
	Hom : Ob -> Ob -> Finite;
	id : Π (X : Ob), Fin (Hom X X);
	comp : Π (X Y Z : Ob), Fin (Hom Y Z) -> Fin (Hom X Y) -> Fin (Hom X Z);
	axiom id_right : ∀ (X Y : Ob) (f : Fin (Hom X Y)), comp X X Y f (id X) = f;
	axiom id_left  : ∀ (X Y : Ob) (f : Fin (Hom X Y)), comp X Y Y (id Y) f  = f;
	axiom assoc : ∀ (X Y Z W : Ob) (f : Fin (Hom X Y)) (g : Fin (Hom Y Z)) (h : Fin (Hom Z W)),
                    comp X Z W h (comp X Y Z g f) = comp X Y W (comp Y Z W h g) f
}
```
The definition become a bit bloated because we don't have [implicit arguments](https://coq.inria.fr/refman/language/extensions/implicit-arguments.html). This is a TODO.

[^note]: This is actually a [small category](https://en.wikipedia.org/wiki/Category_(mathematics)#Small_and_large_categories).
But since we are interested in finite things, small categories are plenty big enough.
