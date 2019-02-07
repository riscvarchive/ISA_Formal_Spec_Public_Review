# ISA_Formal_Spec_Public_Review

This site is UNDER CONSTRUCTION.
Please contact 'rsnikhil'  (Rishiyur S. Nikhil, Bluespec, Inc.) for more information.

This is the "root site" for public review of several efforts within
the RISC-V Foundations' ISA Formal Spec Technical Group to formalize
the RISC-V ISA.  More information soon to follow.

----------------------------------------------------------------

# Request for Public Feedback on RISC-V ISA Formal Specifiations

To all who are interested in ISA Formal Specifications, we would like
your feedback on several draft Formal Specifications of the RISC-V
ISA.

("We" = the ISA Formal Spec Technical Group in the RISC-V Foundation).

----------------------------------------------------------------
## Background on "What is a Formal Spec?  Why Have One?"

A RISC-V ISA Formal Spec is a purely functional, formal, simple
specification of the ISA.

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

"simple" means it should be understandable and usable by a wide
audience-- not just experts in formal methods, but also assembly
language programmers, compiler writers, and hardware designers, many
of whom may not have a background in formal methods, and may not have
the interest or time to acquire one.

When finalized, the RISC-V ISA Formal Spec is expected to become *the*
definitive authority on semantics of RISC-V instructions.  It will be
also be used in official Compliance Testing to certify compliance.

----------------------------------------------------------------
## Rationale for this Public Review of Multiple Approaches

The members of this Technical Group are pursuing multiple approaches,
driven as much by their independent research agendas as by a desire to
contribute a spec to the Foundation.  These approaches vary widely in:

- The choice of formal language in which to express the ISA spec, and
    the style in which it is written (which can affect its
    approachability by a wide audience).

- How instruction-level and thread-level concurrency is expressed.
    This "allowed concurrency and non-deteriminism" is critical in the
    specifying advanced implementations (speculation, out-of-order,
    superscalarity, multi-core, etc.) and their interactions with weak
    memory models such as RISC-V's RVWMO and Ztso.

- The uses to which the spec may easily be applied
    (connectivity to and manipulability by other formal tools).

The RISC-V Foundation needs to select one of the current approaches as
the "official" one.  This one will be used by the Foundation:
- as *the* definitive authority on semantics of RISC-V instructions
- for offical Compliance Testing
- as the basis for formalizing future ISA extensions
and more.

[Note: This does not mean that the other projects will be abandoned; each
is independely driven by their authors' research goals, and each
is likely to be maintained to be consistent with the selected
official spec.]

Your feedback will be critical in helping us decide which approach
will likely find the widest acceptance and usability (e.g, experts in
formal methods may have quite different preferences from other users).

----------------------------------------------------------------

## Links to the various Formal Specs, For Your Review and Feedback

In this repo you will find a collection of "Project Summary" files,
one per formal spec.  They are all written to the same stylized
structure, to make it easy to compare them, and to structure feedback
about them.  In each of the Project Summaries, you will find links to
their "home" repos (all on GitHub), and detailed guidance on how to
read the specs, how to execute them as RISC-V simulators, how to
extend them, and more.

----------------------------------------------------------------

## How to Provide Feedback

We are using the "Issues" tab of this repo to collect feedback.

You are of course welcome to comment in any way you like, but before
starting a new issue topic, kindly scan the list of already-opened
issues to see if your topic is already a topic of discussion so you
can just add your comment to the thread.

In addition to feedback/comments on the specific section-header topics
of the Project Summaries, we would also like to know about:

- Your use cases: Are you likely to use the ISA Formal Spec? In what way?

- Your general overall impressions (approachability, usability,
    extensibility, suitability for formal methods, longevity, ...)

----------------------------------------------------------------

## Review period

The review starts on February 1, 2019 and ends on March 17, 2019.

The follows the RISC-V Foundation's standard recommendation of a
45-day public review period for each of its specs.

----------------------------------------------------------------
