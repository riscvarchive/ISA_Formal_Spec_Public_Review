# RISC-V ISA Formal Specification

## Name

GRIFT - Galois RISC-V ISA Formal Tools

## Authors

Ben Selfridge, Galois Inc.

## Spec sources snapshot

https://github.com/GaloisInc/grift/

## Spec sources live development (e.g. URL to github repo)

https://github.com/GaloisInc/grift/

## Licence

GNU Affero General Public License v3.0

## Metalanguage (including brief explanation of the metalanguage used)

GRIFT is written in Haskell, a strongly-typed functional programming
language.

## Dependencies on what tools

- Various standard Haskell libraries
- Softfloat C library (http://www.jhauser.us/arithmetic/SoftFloat.html)

## Tool ecosystem: what other work has been/is being done with those tools

GRIFT is a part of the BESSPIN tool suite, currently being developed at
Galois. BESSPIN is a collection of tools to understand, explore, and reason
about the correctness and security of a hardware product line. GRIFT is used
within BESSPIN as the golden ISA specification. Currently, it is used for
coverage evaluation of the RISC-V Compliance Working Group's evolving compliance
test suite; in this capacity, it has exposed several shortcomings in many of
those hand-written tests. The GRIFT library will also be used for custom
correctness test generation, based on a particular RISC-V platform
configuration, as well as tandem verification, in joint work with Bluespec.

## Motivation

Goals that drove the development of the GRIFT RISC-V specification:
- Written in a practical programming language capable of providing a strong
theoretical foundation (Haskell)
- Elegant, bidirectional specification of instruction encodings
- Customizable spec, parameterized over the RISC-V feature model (RV[32|64|128]
  [I|M|A|F|D|C])
- Deeply-embedded instruction semantics, written in an embedded domain-specific
language (DSL)
- Designed to be highly compatible with existing tools and frameworks

## Current functional coverage

- RV32/64GC
- Instruction encodings
- Semantics
- Privilege mode M (no S/U yet, that will come Spring 2019)

## Current specification of assembly syntax and encoding

GRIFT does not have a RISC-V assembly parser, but it does have a pretty-printer
that emits individual lines of assembly for a given instruction. Binary
instruction encodings are specified declaratively; the "format" of the
instruction, which determines exactly which bits are "opcode" vs. "operand"
bits, is contained as part of the type of the instruction/opcode, and this same
format is used to determine how to handle encoding and decoding for that
instruction. The mechanism that performs the encoding/decoding, based on Haskell
`lens`es, is handled by our `bv-sized` library (open source on Github).

## Current treatment of concurrency

Concurrency is not currently handled by GRIFT, and as of right now, simulation
with GRIFT is entirely deterministic. We can model concurrency in a
straightforward way in simulation by modifying our step function to produce a
list of potential next-states rather than just one; however, what would be more
compelling and usable would be to define the RISC-V memory model with the
embedded GRIFT semantics DSL (in much the same way as the instructions semantics
themselves are defined). We have not explored such an approach in depth, but it
is very likely an area of further development that will be explored at some
point here at Galois.

## Current treatment of floating-point

Floating point operations are represented in our DSL as symbolic operations on
bitvectors (F32Add, F64Sqrt, etc.), and are interpreted in simulation foreign
function calls to the Berkeley Softfloat library (referenced above).

## Current capabilities

- Haskell library with RISC-V base ISA and extensions, with all instruction encodings
  and semantics for RV32/64GC
- Command-line documentation tool (`grift-doc`)
- Command-line simulation and coverage tool (`grift-sim`)

### Emulation

GRIFT is a Haskell library and can be incorporated as such into any
Haskell codebase. However, it also comes with an executable `grift-sim`, runs
ELF files targeting any standard RISC-V configuration (any of the G/C
extensions, and 32- or 64-bit). `grift-sim` also has been engineered to keep
track of, not only which instructions are covered during simulation, but also
which semantic branches of each instruction are executed. For instance, if an
instruction branches based on whether a particular register identifier is equal
to 0 or not, `grift-sim` will report whether both of those branches have
actually been explored by a particular test or suite of tests.

### Use as test oracle in tandem verification

GRIFT is currently being incorporated into Bluespec's Security Verification
Factory (SVF) as the golden reference model for tandem verification. That work
is still in progress.

### Theorem-prover definitions that support proof

None; however, because the semantics are written in a Haskell-embedded DSL, it
is straightforward to emit theorem-prover definitions for any given target
environment (Coq, ACL2, etc.).

### Use in documentation

GRIFT can automatically generate documentation for each instruction; right now
it is pure textual form via the command-line tool `grift-sim`, but it would be
straightforward to emit LaTeX as well. The semantics definitions include textual
descriptions of the behavior of each instruction, and the semantics themselves
are also pretty-printed so that the user can visually inspect the DSL behavioral
specification of the instruction, again using the command-line tool.

### Use in test generation

None yet, although GRIFT is ideally suited for such given its internal
representation of the instruction semantics. Galois is well-versed in the
problem of code generation, and we have also coordinated with Cambridge
regarding potentially combining our efforts with theirs.

### Use for concurrency-model litmus test evaluation

None.

## Current test coverage

### RISC-V compliance tests

Currently, the only standard RISC-V compliance tests that GRIFT fails are the
ones that make certain assumptions about a particular system configuration; for
example, misaligned loads and stores are currently allowed in GRIFT without any
exceptions raised, but the compliance test assumes an exception will be raised
on a misaligned memory access.

### OS boot testing

We have not attempted to boot Linux yet as we will not have funding for this
work for a few months, although this will be tackled at some point in the near
future.

### Concurrency litmus test testing

None.

### Other

We pass all the standard tests in Berkeley's riscv-tests suite.

## Plans for future functional coverage

- Full privileged architecture, with associated parameterizability
- Automatic test generation via GRIFT semantics and machine code synthesis
  (hopefully to be incorporated in the Compliance WG's infrastructure
- Integration with at least one theorem prover
- Integration with HDLs (e.g., emit verilog code from semantics)
- Concurrency -- nondeterminism in simulation in combination with precise
  specification of RISC-V memory model
- Linux/FreeRTOS boot

## Plans for long-term access, maintenance, etc.

- GRIFT will be maintained in-house at Galois, with Ben Selfridge as the primary
  developer & maintainer. It was designed in part to be highly compatible with
  our codebases, and has become the de facto RISC-V specification
  here. Therefore, we will be expanding and maintaining it for our own internal
  purposes.

## Example instructions

### ADDI

Source code semantics:
```
comment "Adds the sign-extended immediate to register x[rs1] and writes the result to x[rd]."
comment "Arithmetic overflow is ignored."

rd :< rs1 :< imm12 :< Nil <- operandEs

let x_rs1 = readReg rs1
let res   = x_rs1 `addE` (sextE imm12)

assignGPR rd res
incrPC
```

Semantics as displayed by `grift-doc` documentation tool:
```
add semantics
=====================

Adds register x[rs2] to register x[rs1] and writes the result to x[rd].
Arithmetic overflow is ignored.
IF rd == 0x0
  THEN
  ELSE
    x[rd] := (if rs1 == 0x0 then 0x0 else x[rs1]) + (if rs2 == 0x0 then 0x0 else x[rs2])
pc := pc + step
```

## Documentation for model and tools

### Reading Guide

See [docs/Reading.md](https://github.com/GaloisInc/grift/blob/master/docs/Reading.md)
for a discussion of how to read GRIFT as an ISA manual.

### Compiling/Running

See [README.md](https://github.com/GaloisInc/grift/blob/master/README.md) for
information on how to build and run GRIFT on your system.

### Extending

See [docs/Extend.md](https://github.com/GaloisInc/grift/blob/master/docs/Extend.md)
for a discussion of how to extend GRIFT with a new ISA extension.
