# RISC-V ISA Formal Specification

## Name

Forvis ("FOrmal RiscV Specification")

----------------------------------------------------------------
## Authors (with institutions)

Rishiyur S. Nikhil, Bluespec, Inc.

----------------------------------------------------------------
## Spec sources snapshot (e.g. URL to github repo)

[https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec](https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec)

----------------------------------------------------------------
## Spec sources live development (e.g. URL to github repo)

[https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec](https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec)

----------------------------------------------------------------
## Licence

MIT License

----------------------------------------------------------------
## Metalanguage (including brief explanation of the metalanguage used)

Haskell, the well known pure functional language with expressive type
    system and strong static typechecking.  More at
    [haskell.org](https://www.haskell.org).

----------------------------------------------------------------
## Dependencies on what tools

To build and run the spec as a RISC-V simulator, without
floating-point (RISC-V "F" and "D" extensions), you just need:

- `ghc` Haskell compiler.  Available as standard package install on most
    Unix-like systems (e.g., `apt-get install ghc`). More at
    [haskell.org](https://www.haskell.org).

- Haskell's `elf` library for reading ELF compiled RISC-V binaries.

To run the supplied Python program that runs regression tests over all the standard ISA tests:

- Python 3 (version 3.6 or later preferred)

If you include Floating Point in your build, the following git
submodules will have to be pulled:

- Galois, Inc's [Haskell wrapper](https://github.com/GaloisInc/softfloat-hs.git)
    for Berkeley's "softfloat" (see next)

- U.C.Berkeley's "softfloat" IEEE floating point emulation library:
  [softfloat](https://github.com/ucb-bar/berkeley-softfloat-3.git) and
  [testfloat](https://github.com/ucb-bar/berkeley-testfloat-3.git)

----------------------------------------------------------------
## Tool ecosystem: what other work has been/is being done with those tools

- Haskell is a general purpose language used widely in thousands of
    applications.  `ghc` is the most commonly used Haskell compiler.

- Python is a general purpose language used widely in thousands of applications.

- Berkeley "softfloat" is written in C/C++ and is used widely in
    industry and academia to emulate IEEE floating point operations and as
    a "reference model" for IEEE floating point.

----------------------------------------------------------------
## Motivation (a few sentences explaining for what the authors have developed this spec)

Highest priority:

- Readability by people not familiar with formal
    specifications or with Haskell, and who simply want to consult a
    formal spec to get precise answers to questions about ISA
    semantics.  Typical audience: assembly language prorammers,
    compiler writers, CPU hardware designers, RISC-V compliance-test
    writers, and so on.

Other standard motivations:

- Ease of use in formal reasoning with simple connection to other
      formal tools (e.g., proof assistants, theorem provers)
- Ease of building and execution as a simulator, and use as "Golden
    Reference Model" for Compliance Testing and Tandem Verification.
- Ease of extension for formalization of new ISA features
- Ease of extension to plug in formalizations of I/O devices and accelerators
- Same spec code usable both in simple, sequential
    (one-instruction-at-a-time) case as well as more complex
    concurrency (to model pipelines, copies of data in
    caches/store-buffers, wide-issue, out-of-order, multi-core, ...)

----------------------------------------------------------------
## Current functional coverage (what's modelled now, in RISC-V code and in English)

### Base ISA and extensions

Base ISAs: RV32I, RV64I

Standard extensions:
M (Integer Mult/Div),
A (Atomics),
FD (single- and double-precision floating point),
C (Compressed)

### Privilege levels

Privilege levels: M (machine), U (User) and S (Supervisor).

Virtual Memory Schemes: Sv32, Sv39, Sv48

Interrupts: External, Timer and Software interrupts, including
    interrupt delegation.  Non-Maskable Interrupts.

Forvis can model simultaneous RV32 and RV64 (MISA.MXL, MSTATUS.SXL and
MSTATUS.UXL can be set to different values, even dynamically).

----------------------------------------------------------------
## Current specification of assembly syntax and encoding

Forvis does not do anything with respect to assembly syntax, nor are there any plans to do so.

Forvis executes with 32-bit (standard) and 16-bit (C) RISC-V
instructions in memory; the simulator has support to load memory from
ELF files and Memory-Hex (standard Verilog format) files.

Forvis has internal data structures for decoded instructions.  Decoder
and encoder functions to/from 32b/16b representations are provided.

----------------------------------------------------------------
## Current treatment of concurrency

None as yet.

It has always been our objective that the same spec source code can be
*interpreted* in different ways, either (1) sequentially
(one-instruction-at-a-time) or (2) concurrently (to model pipelines,
copies of data in caches/store-buffers, wide-issue, out-of-order,
multi-core, ...).

Currently, compilation and execution as a Haskell program provides (1).

For (2), we have a specific plan for a concurrent interpreter, but
implementing this is future work.  This includes linking it to
RISC-V's weak memory models (RVWMO and ZTSO).

----------------------------------------------------------------
## Current treatment of floating-point

Uses U.C.Berkeley's "softfloat" IEEE floating point emulation library
(which is written in C/C++), accessed via Galois, Inc's Haskell
wrapper.  See links above in Section "Dependencies on what tools"

----------------------------------------------------------------
## Current capabilities (what the specification and associated tooling generates and enables)

### Emulation

Forvis is written in standard, vanilla Haskell.  It can be compiled
with `ghc`, the standard Haskell compiler, to produce a simulator
implementing sequential, one-instruction-at-a-time semantics.  The
simulator can load memory from run ELF or Mem-hex files, and run the
RISC-V program.  The "system" setup provided with Forvis includes:

- A Boot ROM
- A Memory
- Implementation of MTIME and MTIMECMP memory-mapped timer locations,
    capable of generating timer interrupts.
- Implementation of a memory-mapped "software-interrupt" location,
    capable of generating software interrupts.
- A UART (modeling a standard NS16550 UART) through which the CPU can do console IO.

With verbosity 1, execution will provide an instruction trace
(instruction number, PC, instruction, decoded instruction).

With verbosity 2, execution will, in addition, produce a dump of the
full processor state (not memory) after each instruction (PC, GPRs, FPRs, GPRs).

Forvis contains various simulation aids to signal "end of simulation",
such as watching a "tohost" memory address (this is how the ISA tests
are written), stopping at a BREAK instruction, stopping after a given
number of instructions have executed, etc.

There is also a facility to dump out a memory region at "end of
simulation" (e.g., this is how Compliance Tests work).

Speed: Linux kernel boot takes about 40 minutes on a fast laptop.
FreeRTOS kernel boot takes just a few seconds.  This is very fast for
a formal spec; slower than purpose-built C/C++ simulators such as
Spike; and much faster than RTL simulation of most CPU
implementations.

### Use as test oracle in tandem verification

Bluespec uses Forvis as a "Golden Reference Model" for Tandem
Verification of its hardware CPU implementations.  The hardware
produces a "tandem verification trace", and a variant packaging of
Forvis checks this, instruction by instruction, for correctness.  This
includes interrupt-handling.

### Theorem-prover definitions that support proof

Researchers at U.Penn are currently using Forvis as a basis for their
 work to formally prove correctness of their hardware implementations
 as well as their RISC-V security extensions.

### Use in documentation

A general-purpose Python tool is provided in the Doc/ directory to
extract labelled fragments of the spec Haskell source code and to wrap
them in LaTeX environments.  Currently this facility is used in
producing the "Forvis Reading and Extension Guide" (in the Doc
directory).  In principle this facility could also be used to insert
formal spec code fragments into the official English-language spec
docs.

### Use in test generation

Forvis has not yet been used by the author for automated test generation.

Since it contains a data type for decoded instructions, it should be
possible to use Haskell's "QuickCheck" facility to automatically
generate test instruction sequences, but this is still future work.

Researchers at U.Penn are using Forvis for test generation, and for
formal analysis of security properties in their RISC-V extension for
security.

### Use for concurrency-model litmus test evaluation

None yet.

Concurrency in Forvis is still future work.

### Other

We expect that U.Penn's `hs-to-coq` tool can be used to translate
Forvis into "very similar" Coq for subsequent formal analysis and
manipulation in Coq.

A standalone parser for Forvis, written in in Haskell, is in progress.
This will read Forvis source code (which is in Haskell syntax) and
dump out an abstract syntax tree, probably in Lisp-like S-Expression
format.  This parser can also be used to check that Forvis is
restricted to our ``extremely elementary'' subset of Haskell.

----------------------------------------------------------------
## Current test coverage

Forvis passes all ISA tests for the supported features (RV32/RV64
IMAFDC, Privilege levels M/S/U).  A way to run all ISA tests is
provided in the repository.

### RISC-V compliance tests

Forvis passes all currently available tests in the Compliance Suite.
A way to run all Compliance tests is provided in the repository.

### OS boot testing

Forvis boots a Linux kernel compiled for RV64IMASU.  This example is
provided in the repository.  It takes  about 40 minutes on a fast laptop.

Forvis boots a FreeRTOS kernel compiled for RV32IMACU.  This example
is provided in the repository.  It takes just a few seconds.

### Concurrency litmus test testing

None, to date (awaits writing the concurrent interpreter for the Forvis spec code).

### Other

Miscellaneous programs compiled from C using the RISC-V toolchain.

----------------------------------------------------------------
## Plans for future functional coverage

- Basic functionality: Since Forvis already implements both the
    standard bases (RV32I/RV64I) all the standard extensions (IMAFDC),
    all the standard privilege levels (M, S, U) and all the standard
    virtual memory schemes (Sv32, Sv39, Sv48), there are no plans to
    add more functionality.

- We plan to develop the concurrent interpreter to model concurrency,
    as explained in Section "Current treatment of concurrency"

- A work-in-progress is "RIFFL" (RISC-V Formal Feature List) which
    captures all the choices allowed for implementations, such as:

    - whether or not to trap on misaligned memory accesses
    - treatment of A and D bits in Page Table Entries
    - treatment of WARL ("Write Any Read Legal") fields in CSRs
    - ... and so on.

    Currently Forvis makes a specific choice regarding each of these.
    With RIFFL, Forvis will be parameterized to accommodate any
    profile of choices.

----------------------------------------------------------------
## Plans for long-term access, maintenance, etc.

Open source, MIT license, on GitHub.

The tool is used by the author and his company (Bluespec, Inc.) for
tandem verification, as a golden reference model to check Bluespec's
hardware designs, for coverage measurement, and more.  Bluespec and
the author intend to maintain this for the foreseeable future.

----------------------------------------------------------------
## Example instructions

### ITYPE (or ADDI)

Part of the data structure for decoded instructions:

        data Instr_I = LUI    GPR_Addr  InstrField              -- rd,  imm20
                     | ADDI   GPR_Addr  GPR_Addr  InstrField    -- rd,  rs1, imm12
                     | ...

Opcode family of which ADDI is a member:

        opcode_OP_IMM    = 0x13  :: InstrField    -- 7'b_00_100_11

Sub-opcode definition for ADDI:

        -- opcode_OP_IMM sub-opcodes
        funct3_ADDI      = 0x0   :: InstrField    -- 3'b_000

Part of the decode function for ADDI:

        m_instr_I
          | opcode==opcode_OP_IMM, funct3==funct3_ADDI  = Just  (ADDI  rd rs1 imm12_I)

Semantic functions for ADDI (dispatches to a shared function for `OP_IMM`

        exec_ADDI   is_C  (ADDI   rd  rs1  imm12)  mstate = exec_OP_IMM  alu_add   is_C  rd  rs1  imm12  mstate

Semantic functions for `OP_IMM` functions

        exec_OP_IMM :: (Int -> Integer -> Integer -> Integer) -> Bool -> GPR_Addr -> GPR_Addr -> InstrField -> Machine_State -> Machine_State
        exec_OP_IMM    alu_op                                    is_C    rd          rs1         imm12         mstate =
          let
            xlen    = mstate_xlen_read  mstate
            rs1_val = mstate_gpr_read  rs1  mstate

            s_imm   = sign_extend  12  xlen  imm12

            rd_val  = alu_op  xlen  rs1_val  s_imm
            mstate1 = finish_rd_and_pc_incr  rd  rd_val  is_C  mstate
          in
            mstate1


### SRET

In Forvis a single function `exec_xRET` is used for MRET, SRET and URET:

        type Spec_Instr_Priv = Bool -> Instr_Priv -> Machine_State -> Machine_State
        --                     is_C    instr_Priv    mstate           mstate'

        exec_xRET :: Instr_32b -> Spec_Instr_Priv
        exec_xRET    instr_32b    is_C  instr_Priv  mstate =
          let
            is_MRET  = (instr_Priv == MRET)
            is_SRET  = (instr_Priv == SRET)
            is_URET  = (instr_Priv == URET)
            priv     = mstate_priv_read  mstate
            is_legal = ((    is_MRET && (priv == m_Priv_Level))
                         || (is_SRET && (priv >= s_Priv_Level))
                         || (is_URET && (priv >= u_Priv_Level)))

            mstatus   = mstate_csr_read  csr_addr_mstatus  mstate
            tsr_fault = (is_SRET && (priv == s_Priv_Level) && (testBit  mstatus  mstatus_tsr_bitpos))
            (mpp,spp,mpie,spie,upie,mie,sie,uie) = mstatus_stack_fields  mstatus
            rv        = mstate_rv_read   mstate
            misa      = mstate_csr_read  csr_addr_misa  mstate

            mstate3   = if (tsr_fault)
                        then
                          let tval = instr_32b
                          in
                            finish_trap  exc_code_illegal_instruction  tval  mstate

                        else
                          let
                            -- New 'previous-priv' is U if supported, else M
                            new_pp = if (misa_flag  misa  'U') then u_Priv_Level else m_Priv_Level

                            -- New priv, and new priv stack
                            (priv',mpp',spp')
                              -- From M
                              | (priv == m_Priv_Level) && is_MRET && (mpp == m_Priv_Level) = (m_Priv_Level, new_pp, spp)
                              | (priv == m_Priv_Level) && is_MRET && (mpp == s_Priv_Level) = (s_Priv_Level, new_pp, spp)
                              | (priv == m_Priv_Level) && is_MRET && (mpp == u_Priv_Level) = (u_Priv_Level, new_pp, spp)

                              | (priv == m_Priv_Level) && is_SRET && (spp == s_Priv_Level) = (s_Priv_Level, new_pp, spp)
                              | (priv == m_Priv_Level) && is_SRET && (spp == u_Priv_Level) = (u_Priv_Level, new_pp, spp)

                              | (priv == m_Priv_Level) && is_URET                          = (u_Priv_Level, new_pp, spp)

                              -- From S
                              | (priv == s_Priv_Level) && is_SRET && (spp == s_Priv_Level) = (s_Priv_Level, mpp, new_pp)
                              | (priv == s_Priv_Level) && is_SRET && (spp == u_Priv_Level) = (u_Priv_Level, mpp, new_pp)

                              | (priv == s_Priv_Level) && is_URET                          = (u_Priv_Level, mpp, new_pp)

                              -- From U
                              | (priv == u_Priv_Level) && is_URET                          = (u_Priv_Level, mpp, spp)

                            -- New interrupt-enable stack in new mstatus
                            (mpie',spie',upie',mie',sie',uie') | is_MRET = (   1, spie, upie, mpie,  sie,   uie)
                                                               | is_SRET = (mpie,    1, upie,  mie, spie,   uie)
                                                               | is_URET = (mpie, spie,    1,  mie,  sie,  upie)
                            mstatus' = mstatus_upd_stack_fields  mstatus  (mpp',spp',mpie',spie',upie',mie',sie',uie')

                            -- New PC
                            pc1 | is_MRET = mstate_csr_read  csr_addr_mepc  mstate
                                | is_SRET = mstate_csr_read  csr_addr_sepc  mstate
                                | is_URET = mstate_csr_read  csr_addr_uepc  mstate
                            pc2 | (rv == RV32) = (pc1 .&. 0xFFFFFFFF)
                                | True         = pc1

                            -- Update arch state
                            mstate1 = mstate_csr_write   csr_addr_mstatus  mstatus'   mstate
                            mstate2 = mstate_priv_write  priv'  mstate1
                          in
                            finish_pc  pc2  mstate2
          in
            mstate3

----------------------------------------------------------------
## Documentation for model and tools

### snapshot of "Reading Guide", for those who just want to read it like an ISA manual

Please see [Forvis Reading and Extension Guide](https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec/blob/master/Doc/forvis_reading_guide.pdf)

The code, together with this reading guide, is intended to be readable
on its own, without any prior knowledge of Haskell.

### snapshot of "How to Compile/Run Guide" for those who want to execute a model on programs (ISA tests, Compliance tests, other programs)

Please see [README](https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec/blob/master/Doc/README) in the Forvis repository for detailed instructions on how to build and run Forvis on ISA tests, on the Compliance Test Suite, on some C programs, on a Linux kernel, and on a FreeRTOS kernel.

The Linux kernel boot takes about 40 minutes on a fast laptop.

The FreeRTOS kernel boot takes a few seconds.

### snapshot of "How to Extend Guide" for those who want to extend the model to capture new ISA extensions/experiments.

Please see [Forvis Reading and Extension Guide](https://github.com/rsnikhil/Forvis_RISCV-ISA-Spec/blob/master/Doc/forvis_reading_guide.pdf) in the Forvis repository.
