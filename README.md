This site is UNDER CONSTRUCTION.

Please contact 'rsnikhil' (Rishiyur S. Nikhil, Bluespec, Inc.) for more information.

Actual README starts below.

----------------------------------------------------------------
# ISA Formal Spec Public Review

To all who are interested in ISA Formal Specification for RISC-V, we (the ISA Formal Spec Technical Group in the RISC-V Foundation) invite your
feedback on several draft Formal Specifications of the RISC-V ISA. We
are interested in your opinions on how useful you would find each of
the approaches represented here. 
To date, these have been developed by various subgroups, each for their own purposes and with their own priorities.

This site contains (in addition to this README):

- A summary of each of the approaches. The summaries are all written
  to a common template, for easier side-by-side comparison:

    - [GRIFT](GRIFT.md)   (from Galois, Inc.)
    - [Forvis](Forvis.md)  (from Bluespec, Inc.)
    - [Sail](Sail.md)    (from the University of Cambridge, UK, and SRI International)
    - [riscv-plv](riscv-plv.md)  (from MIT)
    - [Kami](Kami.md)    (from SiFive)

- A [comparison table](comparison_table.md) showing features in a tabular form.

Each summary has a link to the project's home web site (all on GitHub)
where you can explore that project's spec in full detail.  Each
project site, in addition to containing the spec itself, also has
documentation on:

- how to read the spec, even if you are
    unfamiliar with the host language in which the spec is written;

- how to execute the spec (i.e., how to run
    RISC-V binaries using the spec as a simulator/emulator);

- how to extend the spec (for future ISA extensions);

- status and plans.

----------------------------------------------------------------
## When and How to Provide Feedback, and What Happens Next

The Public Review period is: March 21, 2019 through May 5, 2019
(standard 45 days recommended by the RISC-V Foundation).

There are two ways to provide feedback (please at least fill in the questionnaire):

- A short, structured [questionnaire](https://docs.google.com/forms/d/e/1FAIpQLSe7jQx_IM2EqvHewuAKiROZXupwsqRu_pCgc-5d0y26DKph7A/viewform)
    (should not take more than a few minutes).

- If you wish, you can provide more expansive free-form comments using the "Issues" tab of this GitHub repo.

We are particularly interested in:

- Your use cases: Are you likely to use the ISA Formal Spec? In what way?

- Your general assessment of how each of the five approaches might meet your needs.

At the end of the feedback period, the ISA Formal Spec Technical Group
will respond to each issue raised, and will make a recommendation to
the RISC-V Foundation about which of the five specs should be adopted
as the "official" spec.

An immediate use of the formal spec is to be the Golden Reference
Model against which "Compliance" is measured by the RISC-V Foundation.

Over time, the spec is expected to be maintained and to grow to
include future official features/extensions of the RISC-V ISA.


-------------------------------------------------------------
## Scope

The initial target for the group is the following set of features only:

- RV32 and RV64

- Base instruction set (I), Integer Multiply Divide (M), Atomics (A),
    Single- and Double-precision floating point (F, D), Compressed
    instructions (C).

- Privilege modes M (machine), S (Supervisor) and U (User), including
    Sv32, Sv39 and Sv48 virtual memory schemes; traps and interrupts;
    Zicsr (CSRs); Zifencei.

- Interaction with the RISC-V Memory Model.

The spec is expected to be extensible in the future to cover other
standard ISA features that are still in development within the RISC-V
Foundation (e.g., Vector, Bit Manipulation, Crypto, J, SIMD, ...).
The specs are all open-source, so they may also be extended by others
for their own custom ISA extensions (some activities like this are
already in progress).

All the five models here are works in progress, and so may not be
ready with all the above features.  Each project's Summary page and
home site describes status and plans for the future.

----------------------------------------------------------------
## Background on "What is a Formal Spec?  Why Have One?"

A RISC-V ISA Formal Spec is a purely functional, precise and formal,
simple specification of the ISA.

"Purely functional" means that it just describes the functional
semantics of instructions, and does not try to capture implementation
properties such as performance, area, power, etc.  As such, it plays a
similar role as the text specification of the ISA, as well as a purely
functional simulator for RISC-V binaries.

"Precise and formal" means that it is written in a formal language
which itself has clear semantics; that it is executable, and that it
is machine readable for manipulation by formal tools.  Of course, it
should be precise, complete, and unambiguous.  (This is where it
differs from a text spec and most production simulators).

"Simple" means it should be understandable and usable by a wide
audience-- not just experts in formal methods, but also assembly
language programmers, compiler writers, and hardware designers, many
of whom may not have a background in formal methods, and may not have
the interest or time to acquire one.

When finalized, the RISC-V ISA Formal Spec is expected to become *the*
definitive authority on semantics of RISC-V instructions.  It will be
also be used in official Compliance Testing to certify compliance.

### Choice of language in which a formal spec is written

Raw execution speed of the formal spec when used as a
simulator/emulator is desirable, but is secondary to the requirements
listed above.  Nevertheless, for some use cases (such as tandem
verification) execution speed needs to be good enough to execute
non-trivial programs (such as booting an OS and running apps under the
OS).

The five approaches each chose their host language for these reasons.
It is very rare to choose, for a formal spec, a large and complex
language that is difficult for formal reasoning (such as C++), or to
write it in a style that favors speed over simplicity and clarity.

Note, some of the five formal specs here use the C++ Berkeley
"softfloat" library to implement IEEE floating point.  This should not
be seen as part of the formal spec, but just as a temporary
placeholder to enable full-system simulation/emulation.  Each
project's documentation may describe future plans for this.

----------------------------------------------------------------
