# Building from source

To build Enumerion you need to install [OCaml](https://ocaml.org/install) and [opam](https://opam.ocaml.org/doc/Install.html).

Once you have OCaml and opam installed, install the dependencies via opam and clone the Enumerion repository
```
opam install dune sedlex menhir menhirLib unix zarith
```

then

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
