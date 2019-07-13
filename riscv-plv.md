# RISC-V ISA Formal Specification

## Name (e.g. Sail Model)

riscv-plv

----------------------------------------------------------------
## Authors (with institutions)

Ian J Clester, Thomas Bourgeat, Andy Wright, Samuel Gruetter, Adam Chlipala (MIT)

----------------------------------------------------------------
## Spec sources snapshot (e.g. URL to github repo)

https://github.com/mit-plv/riscv-semantics

----------------------------------------------------------------
## Spec sources live development (e.g. URL to github repo)

In various branches of

https://github.com/mit-plv/riscv-semantics

----------------------------------------------------------------
## Licence

MIT (Requires update to the files to state it properly, but everyone agreed)

----------------------------------------------------------------
## Metalanguage (including brief explanation of the metalanguage used)

Haskell, a functional programming language that is used widely and well-supported (at least by the standards of functional languages!).
We make an effort to restrict ourselves to a small subset of Haskell features, to ease the construction of tools to translate from our code to other formats automatically.
However, we retain the advantage of an easy path to running the semantics as an interpreter, via the normal Haskell compiler.

----------------------------------------------------------------
## Dependencies on what tools

Haskell, SoftFloat. We pull Haskell dependencies using Stack (a Haskell package manager).

----------------------------------------------------------------
## Tool ecosystem: what other work has been/is being done with those tools

We have made an effort to separate the core specification from "platforms" that can be plugged in to run it in different modes.
Here are some of our uses so far.
- Normal simulation `(rv[32|64]i(m)(a)(f))` with or without IO devices (buffered or not)
- Tandem verification (in a branch), meaning we can run another ISA simulator or a hardware processor simulator in parallel to our semantics, catching where they diverge
- `rv32i` (without any CSRs) compiled as a circuit and model-checked against Clifford Wolf's `rv-formal` (and therefore, transitively, against Spike)

The spec is also transliterated to Coq ([`riscv-coq`](https://github.com/samuelgruetter/riscv-coq)) using [`hs-to-coq`](https://github.com/antalsz/hs-to-coq) and
used to interface with proof of processors ([Kami](https://github.com/mit-plv/kami)) and with proof of compilers.


----------------------------------------------------------------
## Motivation (a few sentences explaining for what the authors have developed this spec)

We wanted a unified way to interact with the different projects
of our group involving RISC-V: proving software in
Coq, proving simple hardware in Coq (Kami), developing
architectures in Bluespec.  Our goal is for the same semantics to be
palatable to partisans of all formal-methods tools (so no bias
towards any one of them) and to practitioners (so no assumption of
background in functional programming or formal methods).

----------------------------------------------------------------
## Current functional coverage (what's modelled now, in RISC-V code and in English)

### Base ISA and extensions

`i,m,f,a`

### Privilege levels

`M,S,U` with more or less testing.
No user-level exceptions.

----------------------------------------------------------------
## Current specification of assembly syntax and encoding

We use normal GCC and load ELF files.
The instructions are represented in an algebraic data type defined in `src/Spec/Decode.hs`.
Here's one example instruction:
    Lb { rd :: Register, rs1 :: Register, oimm12 :: MachineInt }
The same source file contains a decoder from the binary format to syntax trees.

----------------------------------------------------------------
## Current treatment of concurrency

We have designed our semantics to be "pluggable" into different concurrency models.
Basically, we aim to provide a kind of oracle about how a single thread behaves,
parameterized over a platform making such decisions as results of memory operations.
We have made some very preliminary experiments with a simple platform that could
connect to weak memory models, but this potential use of the semantics is not
yet fleshed out.  Another work in progress is examining how to model
memory-mapped IO, but this effort also doesn't have firm conclusions yet.

----------------------------------------------------------------
## Current treatment of floating-point

We piggyback on the SoftFloat library to support the `f` extension.

----------------------------------------------------------------
## Current capabilities (what the specification and associated tooling generates and enables)

### Emulation

Our naive purely functional platform implementation goes at ~100k instructions per second.
We are experimenting with various optimizations, which so far have improved performance by a factor of two.
The modular nature of the semantics allows others to experiment with new platforms without touching the core specification.

### Use as test oracle in tandem verification

We have tandem verification running to compare our semantics against Spike booting Linux.
Again, it requires an alternative platform implementation and depends on no special support in the core semantics.

### Theorem-prover definitions that support proof

[`riscv-coq`](https://github.com/samuelgruetter/riscv-coq) is used as the target semantics of a verified compiler (ongoing work) and for
the export target of Kami (hardware) semantics.

### Use in documentation

We don't do any automatic documentation generation yet, but it has been among our planned backends.

### Use in test generation

None yet, but it seems clear how to write an alternative platform implementation to do some sort of random and/or smart-coverage test generation.

### Use for concurrency-model litmus test evaluation

None yet

----------------------------------------------------------------
## Current test coverage

### RISC-V compliance tests

`rv32im` + `riscv-test`

### OS boot testing

Our emulator currently boots Linux, though it requires a PLIC and a CLINT which
are both in the misnamed branch FastIO.

### Concurrency litmus test testing

None

### Other

We emphasized as much as possible the distinction between
*platform* and *spec*. For example, whether a dirty bit is set in hardware
or software is left to the platform, so our spec expects
a helper from the platform to tell it what choice to make.
Another example is the way the WARL fields are handled.
All this is unspecified in the spec, which calls a handler
that needs to be defined by the platform.


----------------------------------------------------------------
## Plans for future functional coverage

Interaction with the weak memory model by implementing a monad following the operational
semantics for loadW,storeW.

----------------------------------------------------------------
## Plans for long-term access, maintenance, etc.

There's no reason to try to hide that this is an academic project at the moment!
It is hard to predict the precise maintenance trajectory of the semantics or associated tools,
though it would be nice to see the RISC-V Foundation adopt some semantics as blessed
open-source projects, with contributors from various member companies.
However, by building on Haskell, a widely used language with serious maintainers, we at
least avoid being on the hook for a toolset sufficient to run our semantics as an emulator.

----------------------------------------------------------------
## Example instructions

### ITYPE (or ADDI)

```
execute (Addi rd rs1 imm12) = do
  x <- getRegister rs1
  setRegister rd (x + fromImm imm12)
```

### SRET

```

execute Sret = do
  priv <- getPrivMode
  when (priv < Supervisor) (raiseException 0 2)
  tsr <- getCSRField Field.TSR
  when (tsr == 1 && priv == Supervisor) (raiseException 0 2)
  spp <- getCSRField Field.SPP
  setCSRField Field.SPP (encodePrivMode User)
  setPrivMode (decodePrivMode spp)
  spie <- getCSRField Field.SPIE
  setCSRField Field.SPIE 1
  setCSRField Field.SIE spie
  sepc <- getCSRField Field.SEPC
  setPC ((fromIntegral:: MachineInt -> t) sepc)

```

----------------------------------------------------------------
## Documentation for model and tools

### snapshot of "Reading Guide", for those who just want to read it like an ISA manual

It's in `READING.md` at the top level of the repo.

### snapshot of "How to Compile/Run Guide" for those who want to execute a model on programs (ISA tests, Compliance tests, other programs)

It's in `README.md` at the top level of the repo.

### snapshot of "How to Extend Guide" for those who want to extend the model to capture new ISA extensions/experiments.

Sorry, we haven't written one yet.
