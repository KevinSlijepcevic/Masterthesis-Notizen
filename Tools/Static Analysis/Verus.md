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
- https://www.youtube.com/watch?v=7WtWA0TTBqg
	- Benutzt schon neue Syntax

Fortschritt 2023 (Linear Ghost Types):
- https://arxiv.org/pdf/2303.05491.pdf

Paper (long version):

- Kann inline SMT solver parameter einstellen S. 2
- Was fehlt? Return mutable references (Können Prusti, Creusot und Aenas), Seperatate verification of multiple crates S. 3
- Paper erwähnt explizit nicht alle features (User manual dazu checken!)
- ![[Pasted image 20230612120632.png]]

- **Unterstützt KEIN unsafe keyword, its goal is to provide safe replacements for unsafe Rust features, based on linear ghost permissions and SMT based verification** S6
- Bietet eine Art abstraction an für raw pointer pointing into heap mit PPtr und PermData (write, read, alloc, free), diese bilden linear ghost types ab, benutzen nicht unsafe (jedenfalls nicht beim client) und ermöglichen das mit pre/postconditions Kap. 4.1
	- Als Bsp dafür wird eine verifizierte Double Linked List gezeigt die mit PPtr und PermData arbeitet, Kap. 4.2
-  Unterstützt interior mutability kap 5, bietet dafür 2 strategien an:
	-  Use linear ghost state to represent the contents of a cell
		- Bietet analog zu PPtr, PCell an
	- Avoid keeping track of the interior value, wenn der value gelesen wird modelliere das result als nondeterministic, evtl mit invariants gemixt um werte zu begrenzen
		- Bietet den type InvCell an, mit new wird auch ein invariant dafür mitgegeben
		- Invariant muss mein schreiben geproofed werden, beim lesen kann assumed werden das diese eingehalten wird
		- Als Bsp dafür wird eine Memoized Function gezeigt, die einen "cache" über 2 clients teilt, bei dem beide den state des caches ändern können, daher interior mutability in Form von InvCell nötig Kap 5.1
- Unterstützt concurrent datastructures, mit Hilfe von user-defined ghost state Kap 6
	- Als Bsp wird eine FIFO gennant die thread-safe ist und mit hilfe der verus ghost state machinery implementiert wurde
	- Kann auch für single thread hergenommen werden, siehe complimentary material
	- Ist also eine Art state machine?
- Conclusion Kap 12:
	- ""...we’ve found that one of the most valuable tools for verifying Rust code is Rust itself"
- Contributions:
	- Work at CMU was supported, in part, by a gift from VMware, the Alfred P. Sloan Foundation, the Intel Corporation, and the Future Enterprise Security initiative at Carnegie Mellon CyLab (FutureEnterprise@CyLab). At ETH Zurich Andrea Lattuada was supported, in part, by a gift from VMware

User doku:
	- https://verus-lang.github.io/verus/guide/
Support für Rust syntax:
	- https://verus-lang.github.io/verus/guide/features.html
	- https://github.com/verus-lang/verus/wiki/Status%3A-currently-supported-Rust-features
- **Syntax change**!
	- Paper und included Beispiele basiert noch auf alter syntax!
	- https://github.com/verus-lang/verus/wiki/Doc%3A-Deprecated-and-recommended-syntax%2C-and-upcoming-changes
	- Basiert nicht mehr auf fork von rustc: https://github.com/verus-lang/verus/tree/main_new
- Features:
	- Over/Underflow: https://verus-lang.github.io/verus/guide/requires_ensures.html
