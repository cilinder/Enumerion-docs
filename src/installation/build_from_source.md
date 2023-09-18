# Building from source

To build Enumerion you need to install [OCaml](https://ocaml.org/install).

Once you have OCaml installed, clone the Enumerion repository

``` bash
git clone git@github.com:cilinder/Enumerion.git
```

Build it using dune

```
dune build
```

You can now run Enumerion with (dune auto-promotes the executable to the main directory)

```
./enumerion.exe
```
