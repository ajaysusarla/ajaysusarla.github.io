---
layout: post
title: Hello, World in Cogent
date: 
type: post
published: true
status: publish
categories:
- Cogent
tags:
- programming
meta: {}
author:
  login: ajaysusarla
  email: ajaysusarla@gmail.com
  display_name: partha
  first_name: ''
  last_name: ''
---

Ths post talks about writing a "Hello, World" program in Cogent. The
goal behind this exercise is two-fold:

* to give the reader a peek into writing a program in Cogent
* and to given an overview of the different parts of a Cogent program
  and how they fit together
  
Before proceeding further, make sure that the Cogent compiler is built
and installed. Please follow
instructions
[here](https://github.com/NICTA/cogent/blob/master/README.md)
and
[here](https://github.com/NICTA/cogent/blob/master/cogent/INSTALL.md)
to build the Cogent compiler.

So let's get started!

Cogent compiler has
several
[phases](http://ts.data61.csiro.au/images/cogent-overview.png), and
one of the last things it does is generate C code (from the Cogent
code) and invoke a C compiler, to build an ELF object(either an
executable or a kernel module as dictated by the project build rules).
Essentially, the Cogent compiler is tightly coupled with a C compiler.

Cogent relies on C code for entry and exit into a program(or a kernel
module). Typically a Cogent project will have a C file(or in some
cases just an anti-quoted C file) along with Cogent code. The C file
has the entry function into the program(`main()`  for a executable and
`module_init()` for a Linux kernel module), which can invoke Cogent
functions directly(if `main()` or `module_init()` are in a `.ac` file)
or indirectly(if `main()` or `module_init()` are in `.c` file). We
will see, shortly, the difference between a `main.c` and a `main.ac`
file.

## TL;DR

All the code that is discussed here is available in the Cogent
repository
[here](https://github.com/NICTA/cogent/tree/master/cogent/examples/hello-world) along
with a Makefile.

## Structure of a Cogent project

A Cogent 'project' in its simplest of forms has the following files:

* one or more Cogent source files(Cogent source files have a `.cogent`
  extension)
* one or more anti-quoted C source files(anti-quoted C source files
  have `.ac` extension)
* a text file listing all the Cogent functions, each separated by a
  newline, that are called from the anti-quoted C source files.
* an optional(but highly recommended) `Makefile`.

So at a bare minimum, there are 4 files in a Cogent project. Let us
now see how this maps to the "Hello, World" project we are about to
implement.

## Cogent idiosyncrasies

There are a few idiosyncrasies that one needs to get used to when
writing Cogent code. I like to highlight these two:

* Cogent source files cannot be empty. The Cogent compiler just
  doesn't like it.
* All functions in Cogent take only one argument. This is true of most
  functional
  languages(read [currying](https://en.wikipedia.org/wiki/Currying)),
  but this needs a bit of getting used to, especially if one isn't
  familiar with functional programming laguages.

## Hello, World!

Let us start with 4 empty files in our project:

* `Makefile`
* `main.ac`
* `HelloWorld.cogent`
* `entrypoints.cfg`

For
now,
[use](https://gist.github.com/ajaysusarla/0502bad26f5b4bfa1c08829b98618e31#file-makefile) as
the template for the Makefile. The source file names could be anything,
just make sure that the `Makefile` reflects those names. We will go
through the Cogent compiler options in later section.

The first version of `main.ac` will look like this:
```
int main(void)
{
        return 0;
}
```

Since a Cogent source file cannot be empty, add the following
lines in `HelloWorld.cogent`:
```
include <gum/common/common.cogent>
```

Running `make` in the project directory, should spew a lot of
warnings(ignore them for now) and generated a `hello-world`
exectuable. Which, when run, will do nothing.

### Libgum

The included
file[common.cogent](https://github.com/NICTA/cogent/blob/master/cogent/lib/gum/common/common.cogent) in
`HelloWorld.cogent` has some standard abstract data types and
functions that can be used in any Cogent project and is part
of
[libgum](https://github.com/NICTA/cogent/tree/master/cogent/lib/gum)
which provides various abstract data types and functions that can be
used to write Linux kernel modules among other things.
The `include` keyword in Cogent, works like the C include directive
It looks for the file that is being included in the standard path. The
standard include path for libgum on your machine can be found out by
running `cogent --stdgum-dir`.
```
$ cogent --stdgum-dir
/home/partha/cogent/cogent/.cabal-sandbox/share/x86_64-linux-ghc-8.0.1/cogent-2.0.8.0/include/
$
```
And similar to the C, the `include` in Cogent can be used either with
`include "file.cogent"`, to include your own cogent code and `include
<file.cogent>` to include the cogent code in `libgum`.

### HelloWorld.cogent



{% gist ajaysusarla/0502bad26f5b4bfa1c08829b98618e31 Makefile %}
