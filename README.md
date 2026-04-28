# Corten-Core — Basalt

Generation 1. TSMC N7 / GF 28nm. 4.2–4.4 GHz.

Basalt is the first generation of the Corten-Core processor family. 
A clean-slate, security-first, out-of-order superscalar RISC-V processor 
designed for production silicon. No research prototypes. No softcores. 
No excuses.

---

## Specification

| | |
|--|--|
| ISA | RV64GC + Zba, Zbb, Zbc, Zbs |
| Pipeline | 16-stage out-of-order superscalar |
| Decode width | 4-wide |
| ROB | 256 entries · 4-wide allocate · 4-wide commit |
| Physical registers | INT 256 · FP 128 |
| Issue queues | INT 32 · MEM 16 · FP 16 — three independent clusters |
| Execution ports | 11 — ALU ×4 · MUL · DIV · LSU ×2 · FPU · BRU · BITM |
| Branch predictor | TAGE-SC-L — 8 tagged tables · 640-bit GHR · SC · Loop |
| BTB | 8192 entries · 4-way |
| L1I / L1D | 64 KB each · 4-way · VIPT |
| L2 | 1 MB · 8-way · PIPT |
| L3 | 16 MB · 16-way · PIPT · shared across cores |
| Coherence | TileLink2 TL-C · MESI L1/L2 |
| Virtual memory | Sv39 and Sv48 · hardware PTW |
| Privilege levels | M · S · U |
| Cores | 1–4 |
| Node | TSMC N7 / GF 28nm |
| Frequency | 4.2–4.4 GHz |
| TDP | 35W |

---

## Security

Every mitigation is in silicon. No microcode. No OS patch. No compiler flag.

| Threat | Mitigation | Module |
|--|--|--|
| Spectre v1 | STT — tainted results cannot reach cache or address paths before commit | `tainttrak.scala` · `taintgate.scala` |
| Spectre v2 | Per-privilege BTB, RAS, ITTAGE, GHR — IBPB on every transition | `rasstack.scala` · `btbarray.scala` |
| Meltdown | Privilege fence at MEM1 | `privfence.scala` |
| MDS | Synchronous LFB and store buffer clear on privilege transition | `lfbclear.scala` · `stqclear.scala` |
| Spectre v4 | Store-to-load forward suppression on unresolved address overlap | `disambig.scala` · `stfwd.scala` |
| Cache timing | L1D/L1I partitioned by privilege level in hardware | `privpart.scala` |
| Execution timing | MUL 3 cycles · DIV 20 cycles · AMO fixed · BITM 2 cycles | `mulpipe.scala` · `ctdiv.scala` · `ctamo.scala` |
| Speculative PTW | Taint propagation through PTW — tainted TLB entries suppressed before commit | `ptw.scala` · `taintgate.scala` |
| RAS poisoning | Per-privilege RAS — U-mode addresses never enter S-mode RAS | `rasstack.scala` |
| Cross-core timing | L3 partitioned per core · NoC time-multiplexed slots | `l3part.scala` · `nocarb.scala` |

Every security property is formally proved before the covering module ships to integration.

---

## Roadmap

| Name | Gen | Node | Frequency | IPC | Cores | TDP |
|--|--|--|--|--|--|--|
| **Basalt** | 1 | TSMC N7 | 4.2–4.4 GHz | 4.8 | 1–4 | 35W |
| Flux | 2 | TSMC N5 / N4P | 5.4–5.8 GHz | 6.2 | 4–16 | 55W |
| Obsidian | 3 | TSMC N3E | 6.2–6.8 GHz | 7.8 | 8–32 | 80W |
| Temper | 4 | TSMC N2 | 7.2–7.8 GHz | 9.5 | 16–64 | 120W |
| Tungsten | 5 | TSMC A14 | 8.5+ GHz | 12.0 | 32–128 | 180W |

---

## Built With

Chisel 3 · Chipyard · SymbiYosys · OpenRAM · Hammer · Mill · FireSim

---

## Status

Phase 0 — Foundation — in progress.

---

## License

Apache 2.0
