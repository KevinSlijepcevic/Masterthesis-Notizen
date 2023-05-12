https://github.com/verus-lang/verus
- Aktives Development
- Verification of safe Rust Code (mit etwas unsafe Rust und einem Dialect von Rust)
- Fkt und Spezifikationen müssen innerhalb des verus!-Makros benutzt werden, da diese nicht die original Rust-Syntax benutzen
- Overflow/Underflow-Check
- Pre/Post-Conditions und Loop-Invariants mgl.
- Man kann Proof-Funktions anfertigen (lemmas )
- Unterstützt old
- Nachteil: Nicht die komplette Rust-Sprache wird (jemals?) unterstützt
- Aliasing wird durch Rust-Compiler schon verhindert z.B. 
``` rust
let mut account = account; transfer(&mut account, &mut account, 100) // Fehler da 2x mut borrow
```
- Ghost-Code möglich (also Code der nur zur Verifikation benutzt wird, aber nicht compiliert wird)
- Solver-Optionen einstellbar im Code (z.B. by (nonlinear_arith) oder by (bit_vector))

Fortschritt 2023 (Linear Ghost Types):
- https://arxiv.org/pdf/2303.05491.pdf