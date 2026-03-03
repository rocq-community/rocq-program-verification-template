# Rocq Program Verification Template

[![Docker CI][docker-action-shield]][docker-action-link]

[docker-action-shield]: https://github.com/rocq-community/rocq-program-verification-template/actions/workflows/docker-action.yml/badge.svg?branch=master
[docker-action-link]: https://github.com/rocq-community/rocq-program-verification-template/actions/workflows/docker-action.yml

Template project for program verification in the Rocq Prover.
Uses the Verified Software Toolchain and a classic binary
search program in C as an example.

## Meta

- License: [Unlicense](LICENSE) (change to your license of choice)
- Compatible Rocq versions: 9.0 or later
- Additional dependencies:
  - [CompCert](http://compcert.org) 3.16 or later
  - [Verified Software Toolchain](https://vst.cs.princeton.edu) 2.16
- Rocq namespace: `ProgramVerificationTemplate`

## Building instructions

### Installing dependencies

The recommended way to install Rocq and other dependencies is via
the [Rocq Platform](https://github.com/rocq-prover/platform/releases/latest).
To install dependencies manually via [opam](https://opam.ocaml.org/doc/Install.html):
```shell
opam repo add rocq-released https://rocq-prover.org/opam/released
opam install coq-vst.2.16
```

### Obtaining the project

```shell
git clone https://github.com/rocq-community/rocq-program-verification-template.git
cd rocq-program-verification-template
```

### Option 1: building the project using `rocq makefile`

With make and the [rocq makefile tool][rocq-makefile-url] bundled with Rocq:
```shell
make   # or make -j <number-of-cores-on-your-machine> 
```

### Option 2: building the project using Dune

With the [Dune build system][dune-url], version 3.21 or later:
```shell
dune build
```

### Compiling the program using CompCert (optional)

```shell
ccomp -o bsearch src/binary_search.c
```

## File and directory structure

### Core files

- [`src/binary_search.c`](src/binary_search.c): C program that performs binary
  search in a sorted array, inspired by [Joshua Bloch's Java version][binary-search-url].
- [`theories/binary_search.v`](theories/binary_search.v): Rocq representation
  of the binary search C program in [CompCert's Clight language][compcert-c-url].
- [`theories/binary_search_theory.v`](theories/binary_search_theory.v): General
  Rocq definitions and facts relevant to binary search, adapted from code in the
  [Verified Software Toolchain][vst-url].
- [`theories/binary_search_verif.v`](theories/binary_search_verif.v): Contract for the
  Clight program following the [Java specification][java-specification-url] and a
  Rocq proof using the Verified Software Toolchain that the program upholds the contract.

### General configuration

- [`rocq-program-verification-template.opam`](rocq-program-verification-template.opam):
  Project [opam package][opam-url] definition, including dependencies.
- [`_RocqProject`](_RocqProject): File used by Rocq editors to determine the Rocq logical path,
  and by the make-based build to obtain the list of files to include. 
- [`.github/workflows/docker-action.yml`](.github/workflows/docker-action.yml):
  [GitHub Actions][github-actions-ci-url] continuous integration configuration for Rocq,
  using the opam package definition.

### Make configuration

- [`Makefile`](Makefile): Generic delegating makefile using [rocq makefile][rocq-makefile-url].
- [`Makefile.rocq.local`](Makefile.rocq.local): Custom optional Make tasks, including compilation
  of the C program.

### Dune configuration

- [`dune-project`](dune-project): General configuration for the [Dune][dune-url] build system.
- [`theories/dune`](theories/dune): Dune build configuration for Rocq.

## Caveats

### rocq makefile vs. Dune

`rocq makefile` and Dune builds are independent. However, for local development,
it is recommended to use `rocq makefile`, since Rocq IDEs may not be able find
files compiled by Dune. Due to its build hygiene requirements, Dune will
refuse to build when binary (`.vo`) files are present in `theories`;
run `make clean` to remove them.

### Generating Clight for Rocq

The Rocq representation of the C program (`binary_search.v`) is kept in version
control due to licensing concerns for CompCert's `clightgen` tool.
If you have a license to use `clightgen`, you can delete the generated file
and have the build system regenerate it. To regenerate the file manually, you need to run:
```shell
clightgen -o theories/binary_search.v -normalize src/binary_search.c
```

[binary-search-url]: https://research.google/blog/extra-extra-read-all-about-it-nearly-all-binary-searches-and-mergesorts-are-broken/
[java-specification-url]: https://hg.openjdk.java.net/jdk10/jdk10/jdk/file/ffa11326afd5/src/java.base/share/classes/java/util/Arrays.java#l1846
[vst-url]: https://vst.cs.princeton.edu
[compcert-c-url]: https://compcert.org/compcert-C.html
[rocq-makefile-url]: https://rocq-prover.org/refman/practical-tools/utilities.html#building-a-rocq-project-with-rocq-makefile-details
[github-actions-ci-url]: https://github.com/rocq-community/docker-coq-action
[opam-url]: https://opam.ocaml.org
[dune-url]: https://dune.build
