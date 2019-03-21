# RISC-V ISA Formal Specification: Sail Model

## Name

Sail RISC-V model

## Authors (with institutions)

 Prashanth Mundkur, SRI International;
 Jon French, University of Cambridge;
 Brian Campbell, University of Edinburgh;
 Robert Norton, University of Cambridge;
 Alasdair Armstrong, University of Cambridge;
 Thomas Bauereiss, University of Cambridge;
 Shaked Flur, University of Cambridge;
 Peter Sewell, University of Cambridge

## Spec sources snapshot (e.g. URL to github repo)

## Spec sources live development (e.g. URL to github repo)

<https://github.com/rems-project/sail-riscv>


## Licence

BSD two-clause 


## Metalanguage (including brief explanation of the metalanguage used)

This ISA specification is written in [Sail](https://www.cl.cam.ac.uk/~pes20/sail/).

Sail is a language for describing the instruction-set architecture
(ISA) semantics of processors, in an engineer-friendly,
vendor-pseudocode-like style. Sail is essentially a first-order
imperative language, but with lightweight dependent types
(automatically checked using Z3) for the bitvector lengths and integer
ranges that are pervasive in ISA specifications.  Given a Sail
definition, the tool will type-check it and generate executable
emulators, in C and OCaml, theorem-prover definitions for Isabelle,
HOL4, and Coq, and definitions to integrate with our
[RMEM](http://www.cl.cam.ac.uk/users/pes20/rmem) tool for concurrency
semantics.

![Sail overview](http://www.cl.cam.ac.uk/users/pes20/sail/overview-sail.png)

## Dependencies on what tools

Sail is available as pre-built binaries via OPAM, as a source package, and as source in a github repository, BSD licenced. 
It depends on OCaml, OPAM, libgmp-dev, Z3, Lem, and Ott.


## Tool ecosystem: what other work has been/is being done with those tools


Sail is being used for multiple ISA descriptions, including
essentially complete versions of the sequential behaviour of ARMv8.5-A
(automatically derived from the authoritative ARM-internal
specification, and released under a BSD Clear licence with ARM's
permission), RISC-V, MIPS, and CHERI-MIPS; all these are complete
enough to boot various operating systems.  There are also Sail models
for smaller fragments of IBM POWER and x86.

Sail is being used:

- as an ISA design tool for CHERI, both as an executable reference model and for the pseudocode in the CHERI ISA manual
- to make theorem-prover ISA definitions available, for multiple architectures and targetting multiple theorem provers
- to support work on architecture concurrency models


## Motivation (a few sentences explaining for what the authors have  developed this spec)

The Sail RISC-V ISA model has been developed:

- as a basis for the design of a CHERI variant of RISC-V, incorporating hardware support for fine-grain memory protection and secure encapsulation.
- to support our work on the RISC-V concurrency architecture, providing the ISA semantics required by our [RMEM](http://www.cl.cam.ac.uk/users/pes20/rmem) tool for concurrency
semantics, which lets one compute all the concurrency-model-allowed behaviours of small litmus tests or ELF binaries.
- to provide theorem-prover definitions of the RISC-V ISA, for multiple provers
- to support the automatic generation of instruction tests
- to use as a test oracle in tandem verification
- to provide readable pseudocode that could be used in the RISC-V ISA specification

## Current functional coverage (what's modelled now, in RISC-V code and in English)

- The model covers RV32 and RV64 IMAC (base 32-bit and 64-bit integer architectures, with the standard
  multiply/divide, atomic, and compressed extensions), M/S/U (machine,
  supervisor and user privilege) modes, and the Sv32, Sv39, and Sv48 modes
  of address translation.  Operation in M-only, M/U and M/S/U
  modes are all supported.  The model includes a specification for the
  draft 'N' extension for user-level interrupt handling.

- The model supports parameterization for platform-specific implementation choices.

- The model does not currently cover RV128, floating-point for the F and D standard
  extensions, encodings for HINT instructions, PMP/PMA physical memory
  attributes and protection, event and performance counter support,
  the hypervisor mode for virtualization,
  and other extensions such as the vector and bit-manipulation extensions.

## Current specification of assembly syntax and encoding

The model includes an executable specification of the assembly
language encoder/decoder.

## Current treatment of concurrency

The ISA model is integrated with the operational model of the RISC-V
relaxed memory model, RVWMO (as described in an appendix of the [RISC-V
user-level specification](https://github.com/riscv/riscv-isa-manual/releases/tag/draft-20181227-c6741cb)), which is one of the reference models used
in the development of the RISC-V concurrency architecture; this is
part of our [RMEM](http://www.cl.cam.ac.uk/users/pes20/rmem) tool.



## Current treatment of floating-point

The Sail RISC-V model does not currently support floating point.
We plan to add this over the next few months, at least for some of the Sail emulator and theorem-prover targets.  

## Current capabilities (what the specification and associated tooling generates and enables)

### Emulation

The model provides OCaml and C emulators that can execute RISC-V ELF
files, and both emulators provide platform support sufficient to boot
Linux, FreeBSD and seL4.  The OCaml emulator can generate its own
platform device-tree description, while the C emulator currently
requires a consistent description to be manually provided.  The C
emulator can be linked against the Spike emulator for execution with
per-instruction tandem-verification.

The C emulator, for the Linux boot, currently runs at approximately
300 KIPS on an Intel i7-7700 (when detailed per-instruction tracing
is disabled), and there are many opportunities for future optimisation
(our Sail MIPS model runs at approximately 1 MIPS). This enables us to
boot Linux in about 4 minutes, and FreeBSD in about 2 minutes. Memory
usage for the C emulator when booting Linux is approximately 140MB.


### Use as test oracle in tandem verification

For tandem verification of random instruction streams we support the
protocols used in [TestRIG](https://github.com/CTSRD-CHERI/TestRIG) to
directly inject instructions into the C emulator and produce trace
information in RVFI format.  This has been used for cross testing
against spike and the [RVBS](https://github.com/CTSRD-CHERI/RVBS)
specification written in Bluespec SystemVerilog.

The C emulator can also be directly linked to Spike, which provides
tandem-verification on ELF binaries (including OS boots).  This is
often useful in debugging OS boot issues in the model when the boot is
known working on Spike.  It is also useful to detect platform-specific
implementation choices in Spike that are not mandated by the ISA
specification.

### Theorem-prover definitions that support proof

With Sail we aim to support the generation of idiomatic theorem prover
definitions across multiple tools. At present we support Isabelle,
HOL4 and Coq, and provide snapshots of the generated theorem prover
definitions. Typically the verification community has been split
across tool-specific lines, for example in verified compilation the
CakeML project uses HOL4, seL4 uses Isabelle, and CompCert and CertiKOS use Coq. Given the broad
interest in formal ISA specs among this community and others, we
consider the ability to target multiple tools to be of key practical
importance.

For the Sail RISC-V model, we currently generate definitions in HOL4,
Isabelle, and Coq that are accepted by each of those tools.  We have tested
HOL4 Kananaskis-12, Isabelle 2018, and Coq 8.8.1.

For Coq, we generate definitions that preserve most of the
liquid/dependent typing from the Sail specification, whereas for
Isabelle and HOL4 we perform a specialised partial monomorphisation
that retains useful typing information where possible yet avoids
duplicating code unnecessarily.

Our theorem-prover translation can target multiple monads for
different purposes. The first is a state monad with nondeterminism and
exceptions, suitable for reasoning in a sequential setting, assuming
that effectful expressions are executed without interruptions and with
exclusive access to the state.

For reasoning about concurrency, where instructions execute
out-of-order, speculatively, and non-atomically, we provide a free
monad over an effect datatype of memory actions. This monad is also
used as part of our aforementioned concurrency support via the RMEM
tool.

### Use in documentation

Latex definitions can be generated from the model that are suitable
for inclusion in reference documentation.  Drafts of the RISC-V
[unprivileged](https://github.com/rems-project/riscv-isa-manual/blob/sail/release/riscv-spec-sail-draft-alpha.pdf)
and
[privileged](https://github.com/rems-project/riscv-isa-manual/blob/sail/release/riscv-privileged-sail-draft-alpha.pdf)
specifications that include the Sail formal definitions are available
in the sail branch of this [riscv-isa-manual
repository](https://github.com/rems-project/riscv-isa-manual/tree/sail).

### Use in test generation

Our OCaml backend can produce QuickCheck-style random generators for
types in Sail specifications, which we have used to produce random
instructions sequences for testing.  The generation of individual
types can be overridden by the developer to, for example, remove
implementation-specific instructions or introduce register biasing.

Measuring line and branch coverage of the C emulator generated from
the Sail specification, the RISC-V test suite (not the new compliance
suite, because it will need some setup and is mostly RV32) hits 73%
(line) / 64% (branch), while the Sail-generated random tests cover 49%
/ 30%.  This lower coverage is not a surprise: the latter currently
does not generate CSR or AMO related instructions, so is presumably
not hitting the memory and interrupt handling code used by these.

### Use for concurrency-model litmus test evaluation

As part of our concurrency architecture work, we have produced and
released a library of approximately 7000 [litmus
tests](https://github.com/litmus-tests/litmus-tests-riscv).  The
operational and axiomatic RISC-V concurrency models are in sync for
these tests, and they moreover agree with the corresponding ARM
architected behaviour for the tests in common.

We have also run these tests on RISC-V hardware, on a SiFive RISC-V
FU540 multicore proto board (Freedom Unleashed), kindly on loan from
Imperas. To date, we see only sequentially consistent behaviour there.


## Current test coverage

### Basic (riscv-tests) tests 

Yes

### RISC-V compliance tests

Both the C and OCaml emulators support use as a test target in the
compliance-test framework, and we intend to support use as a golden model.

### OS boot testing

The 64-bit C and OCaml emulators have been used to boot Linux and FreeBSD
up to the login prompt, including most of the standard startup scripts
and programs, and the seL4 kernel running its test suite.
OS boots for the 32-bit emulators are in progress.


### Concurrency litmus test testing

The model supports all the litmus tests mentioned above, which are the
only concurrency test collection for RISC-V that we are aware of.

### Other


## Plans for future functional coverage

We plan to add support for the F and D standard
floating-point extensions, support for HINT encodings, PMP/PMA physical memory attributes
and protection.
This may be followed by support for the hypervisor mode.

## Plans for long-term access, maintenance, etc.

Sail is an academic project, developed from 2014 to date.  It is a
central part of our research infrastructure, so we expect to actively
maintain it for the foreseeable future.  It is open-source, BSD
licenced, and developed in a public github repository.

The Sail RISC-V model is necessary for our research on CHERI; it is
also one of the several ISA models that we aim to continue to make
available in multiple theorem-prover targets, to support software and
hardware verification.  We thus also expect to maintain it for the
foreseeable future.  We may not have the resources or motivation to
ourselves define every RISC-V extension in Sail, but that is properly
the task of extension authors, and we would be happy to support them 
to do so.




## Example instructions

These are verbatim excerpts from the main model file, [riscv.sail](https://github.com/rems-project/sail-riscv/blob/master/model/riscv.sail), with a few comments added.

### ITYPE (or ADDI)
~~~~~
/* the assembly abstract syntax tree (AST) clause for the ITYPE instructions */

union clause ast = ITYPE : (bits(12), regbits, regbits, iop)

/* the encode/decode mapping between AST elements and 32-bit words */

mapping encdec_iop : iop <-> bits(3) = {
  RISCV_ADDI  <-> 0b000,
  RISCV_SLTI  <-> 0b010,
  RISCV_SLTIU <-> 0b011,
  RISCV_ANDI  <-> 0b111,
  RISCV_ORI   <-> 0b110,
  RISCV_XORI  <-> 0b100
}

mapping clause encdec = ITYPE(imm, rs1, rd, op) <-> imm @ rs1 @ encdec_iop(op) @ rd @ 0b0010011

/* the execution semantics for the ITYPE instructions */

function clause execute (ITYPE (imm, rs1, rd, op)) = {
  let rs1_val = X(rs1);
  let immext : xlenbits = EXTS(imm);
  let result : xlenbits = match op {
    RISCV_ADDI  => rs1_val + immext,
    RISCV_SLTI  => EXTZ(rs1_val <_s immext),
    RISCV_SLTIU => EXTZ(rs1_val <_u immext),
    RISCV_ANDI  => rs1_val & immext,
    RISCV_ORI   => rs1_val | immext,
    RISCV_XORI  => rs1_val ^ immext
  };
  X(rd) = result;
  true
}

/* the assembly/disassembly mapping between AST elements and strings */

mapping itype_mnemonic : iop <-> string = {
  RISCV_ADDI  <-> "addi",
  RISCV_SLTI  <-> "slti",
  RISCV_SLTIU <-> "sltiu",
  RISCV_XORI  <-> "xori",
  RISCV_ORI   <-> "ori",
  RISCV_ANDI  <-> "andi"
}

mapping clause assembly = ITYPE(imm, rs1, rd, op)
                      <-> itype_mnemonic(op) ^ spc() ^ reg_name(rd) ^ sep() ^ reg_name(rs1) ^ sep() ^ hex_bits_12(imm)
~~~~~~

### SRET 

~~~~~
union clause ast = SRET : unit

mapping clause encdec = SRET() <-> 0b0001000 @ 0b00010 @ 0b00000 @ 0b000 @ 0b00000 @ 0b1110011

function clause execute SRET() = {
  match cur_privilege {
    User       => handle_illegal(),
    Supervisor => if   mstatus.TSR() == true
                  then handle_illegal()
                  else nextPC = handle_exception(cur_privilege, CTL_SRET(), PC),
    Machine    => nextPC = handle_exception(cur_privilege, CTL_SRET(), PC)
  };
  false
}

mapping clause assembly = SRET() <-> "sret"
~~~~~

## Documentation for model and tools

There is  a [manual for Sail](https://github.com/rems-project/sail/blob/sail2/manual.pdf),
which describes the features of the language, provides information
about the various command-line flags and targets, and contains a small
tutorial for ISA description in Sail based on a two-instruction
fragment of RISC-V.

### snapshot of "Reading Guide", for those who just want to read it like an ISA manual

There are two options for a reading guide.  If one intends to
understand some detail of the ISA specification documents, one could
use the Sail-annotated of the RISC-V architecture specifications available in the sail branch of
<https://github.com/rems-project/riscv-isa-manual>:
the [unprivileged](https://github.com/rems-project/riscv-isa-manual/blob/sail/release/riscv-spec-sail-draft-alpha.pdf)
and
[privileged](https://github.com/rems-project/riscv-isa-manual/blob/sail/release/riscv-privileged-sail-draft-alpha.pdf) specs.
If one intends to understand the Sail model itself, one could use the
[ReadingGuide](https://github.com/rems-project/sail-riscv/blob/master/doc/ReadingGuide.md) provided in the model repository.

### snapshot of "How to Compile/Run Guide" for those who want to execute a model on programs (ISA tests, Compliance tests, other programs)

The documentation in the model repository provides basic instructions
to compile and run the C and OCaml emulators on ELF files and OS binaries.

### snapshot of "How to Extend Guide" for those who want to extend the model to capture new ISA extensions/experiments.

A [guide](https://github.com/rems-project/sail-riscv/blob/master/doc/ExtendingGuide.md)
to extending the model is provided in the repository.



## Caveats and limitations (anything potential users should be aware of that is not clear above)

none
