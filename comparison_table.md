A table of the easily summarisable aspects of the RISC-V Formal ISA model comparison.  It should be read together with the linked-to descriptions for each model.  This table describes the current state of the models only, not work in progress and future plans; see the descriptions for those. 

|                                                   | Forvis        | Grift                          | Sail                    | riscv-plv           | Kami       | 
| ------------------------------------------------- | ------------- | ------------------------------ | ------------------------| ------------------- | ---------- |
| Link to description                               | [Forvis](https://github.com/rsnikhil/Temporary_TGISA/blob/master/Forvis.md)    | [Grift](https://github.com/rsnikhil/Temporary_TGISA/blob/master/GRIFT.md)   | [Sail](https://github.com/rsnikhil/Temporary_TGISA/blob/master/Sail.md)                | [Riscv-plv](https://github.com/rsnikhil/Temporary_TGISA/blob/master/riscv-plv.md) | [Kami](https://github.com/sifive/RiscvSpecFormal)          | 
| Author/Group                                      | Bluespec      |Galois                          |SRI/Cambridge            | MIT                 | SiFive     |   
| Licence                                           | MIT           |GPL3                            |BSD                      | MIT                 | Apache 2.0 |
| Metalanguage                                      | Haskell       |Haskell                         |Sail                     | Haskell             | Kami/Coq   |
| Functional coverage - Base ISA and extensions     | RV32/64IMAFDC |RV32/64GC                       |RV32/RV64IMAC            | RV32/64IMAF         | RV32 IMAFC |
| Functional coverage - Privilege levels            | MUS,Sv32,39,48|M                               |MUS,Sv32,39,48           | Sv39                | no         |
| Specification of assembly syntax and encoding     | no            |pp                              |yes                      | no                  | yes        |
| Concurrency                                       | no            |no                              |yes                      | no                  | no         |
| Floating-point                                    | via Softfloat |via Softfloat                   |no                       | via Softfloat       | Native implementation of IEEE 754-2008 |
| Emulation                                         | Haskell       | Haskell                        |generated C or OCaml     | Haskell             | Verilator  |
| ...emulation speed                  | ??? IPS (40min Linux boot)  |40K IPS on Intel Xeon E312      |300K IPS on Intel i7-7700  (4min Linux boot)  | 100K IPS on 6700HQ (Linux boot)     | Not measured |
| Use as test oracle in tandem verification         | yes           |no                              |yes                      | yes                 | yes |
| Use for software coverage analysis                | ???           |yes                             |???                      | ???                 | ??? |
| Theorem-prover definitions                        | via hs-to-coq?|no                              |Coq,Isa,HOL4             | hs-to-coq           | Coq |
| Use in documentation                              | to LaTeX      |to text                         |to LaTeX in RISC-V ISA   | no                  | no |
| Use in test generation                            | (at UPenn?)   |no                              |yes                      | no                  | no |
| Use for concurrency-model litmus test evaluation  | no            |no                              |yes                      | no                  | no |
| Test coverage - riscv-tests suite                 | ???           |yes                             |yes                      | yes                 | yes |
| Test coverage - RISC-V compliance tests           | all           |almost all                      |yes                      | yes                 | yes |
| Test coverage - OS boots                          | Linux,FreeRTOS|no                              |Linux,FreeBSD,seL4       | Linux               | no      |
| Test coverage - Concurrency litmus tests          | no            |no                              |yes                      | no                  | no      |

