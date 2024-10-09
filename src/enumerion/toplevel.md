# Toplevel commands

<!-- toc -->

The syntax for toplevel commands is

```
toplevel_cmd ::= load <filename>
                 | def <varname> := <expr>
                 | check <expr> (: <type>)
                 | eval <expr>
                 | enumerate <type>
                 | axiom <varname> := <type>
                 | clean
```

where `<expr>` and `<type>` are both just Enumerion [expressions](syntax.md#expressions). They are denoted differently because in certain places we expect an expression to be a type itself. As far as the syntax is concerned, there is no difference, this only comes into effect in the [typechecking phase](typechecking.md).

### load

Syntax: `load <filename>`

Load and evaluate all the toplevel commands in file `<filename>`.

##### Example

```enum
load "examples/category.enum"
```

### def

Syntax: `def <varname> := expr`

Evaluate the Enumerion expression `expr` and store it in the variable `<varname>`.
##### Example

```enum
# def f := fun (x : Nat), x + 1
f is defined.
```

### check

Syntax: `check <expr> (: <type>)`

Infer the type of `<expr>`. If `<type>` is given, check that `<expr>` has this type.

##### Example

```enum
# check 4
4
     : Fin 5
```

```enum
# check 4 : Nat
4
     : ℕ
```

### enumerate

Syntax: `enumerate <type>`

Enumerate all elements of the type `<type>`. To do this, compile the expression `<type>` to an
[Enumix](enumerion/enumix/introduction.md) computation and evaluate the resulting program.

##### Example

```enum
# enumerate Fin 3
0
1
2
Size is 3.
```

### compile

Syntax: `compile <type>`

Compile the expression `<type>` to an [Enumix](enumerion/enumix/introduction.md) computation and print the resulting s-expression.

##### Example

```enum
# compile structure { x : Fin 2 }
(bind (range 0 2) (return (record ((field "x" (var 0))))))
```

### eval

Syntax: `eval <expr>`

Evaluate the expression `<expr>` and print the value.

##### Example

```enum
# eval (fun (x : Nat) => x + 2) 3
5
     : ℕ
```

### axiom

Syntax: `axiom <varname> : <type>`

Check that `<type>` is a type and add the assumption that `<varname>` is a value of that type to the context.

##### Example

```enum
# axiom A : 0 = 1
A : 0 = 1 is an axiom.
```

### clear

Syntax: `clear`

Remove all toplevel definitions and axioms from the current context.

##### Example

```enum
# def x := 3
x is defined.
# check x
x
     : Fin 4
# clear
The environment has been reset.
# check x
Syntax error at line 1, characters 6-7:
unknown identifier x
```
