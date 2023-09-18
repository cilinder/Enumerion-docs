# What is Enumerion

Enumerion is a system for defining and enumerating finite discrete mathematical structures.

It is implemented in two parts:
- The core, which is build on a dependent type theory with booleans, natural numbers, Pi-types, record types and propositions.
- The enumeration engine called Enumix, which is a domain specific programming language.

The process of enumerating a structure is done in three steps:
- We define the structure of interest in the type theory of Enumerion.
- We compile the definition to a procedure in Enumix.
- We run the compiled procedure.
