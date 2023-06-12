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
	- Zeigt teils andere Syntax ? verus! macro?

Fortschritt 2023 (Linear Ghost Types):
- https://arxiv.org/pdf/2303.05491.pdf

Paper (long version):
- SMT Tool (Abstract)
- Specifications in Rust (subset) (Abstract) mit zwei neuen integer types: int und nat S.4
- Specifications und Code/Proofs sind getrennt, Specs are not checked for linearity and borrowing (Abstract)
- Erwähnt concurrent: Kann also mit concurrent umgehen? S. 1
- Demonstrate the use of linear ghost permissions, ermöglicht actions on specific resources e.g. memory. Dies existieren nur zur verification and type checking (Ghost). S. 2
- In Specifications nur pure erlaubt ohne Rekursion, in normalen Code auch rekursion erlaubt S. 2
- Unterscheidet Quellcode in 3 Modes (S.2) mit Hilfe von **Annotations** (S.3) : 
	- Specification: Termination, Type check
	- Proof: Termination, Type check, linearity and borrowing
	- Executable: Type check, linearity and borrowing check, compiled to machine code
- Unterstützt viele Rust und Verification features (siehe Auflistung S. 2)
- Kann inline SMT solver parameter einstellen S. 2
- Was fehlt? Return mutable references (Können Prusti, Creusot und Aenas), Seperatate verification of multiple crates S. 3
- Paper erwähnt explizit nicht alle features (User manual dazu checken!)
- Extends the Rust compiler (S. 3), Specs und Proofs sind ghost code
- Pre/Postconditions, Loop Invariants als "normale" Function calls, keine Annotations S4
- Postconditions unterstützen return value S4, kann closure akzeptieren
- Benutzt Z3 S4
- Assertions as hints for SMT solver S4, prinzip ähnlich zu Boogie Dafny F*
- ![[Pasted image 20230612120632.png]]
- spec functions werden 1 zu 1 in SMT functions umcodiert (dh. keine pre/postconditions), unterstützen aber recommends functionen die als "soft" precondition fundieren um dev zu helfen bei fehlern und nur dann ausgewertet werden S5
- Recursive spec and proof functions sind nur valid wenn sie terminieren, wird sichergestellt mit decrease clause
- Für unfodling defenitions of a recursive function, reveal_with_fuel function steuert fuel (?) S5
- Verus trusts borrow checker, wie Creusot S6
- Spec annotation kann auch inline in #[exec] code stattfinden, welche dann nicht borrow gechecked werden, sog. spec variables; sind similar to ghost variables
- Es gibt auch proof variables, mit #[proof] markiert inline, diese sind aber gechecked nach linearity
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
- Implementierung Kap 7
	- Benutzt ein Fork von rustc: Youtube vid sagt dass das bald geändert wird
	- Ist ein rustc driver, wie die anderen Projekte auch
	- Evaluierung vorhanden, Beispielcode ist im complementary material enthalten
- Error handling Kap 8
	- Wie Rust error msgs da Rustc erweiterung
	- Zeigt genau an welche Condition nicht erfüllt wird nicht nur die Line, und liefert auch Context welche Instruktion diese nicht erfüllt
- Limitations Kap 9
	- Keine return mutable borrows, soll bald gefixt werden
	- Unterstützt nur mutable borrows in fkt call ? Prüfen!
	- Da Verus auf den borrow-checker aufbaut, kann es nicht unsafe direkt prüfen, nur die abstractionen die es anbietet
- Conclusion Kap 12:
	- ""...we’ve found that one of the most valuable tools for verifying Rust code is Rust itself"
- Contributions:
	- Work at CMU was supported, in part, by a gift from VMware, the Alfred P. Sloan Foundation, the Intel Corporation, and the Future Enterprise Security initiative at Carnegie Mellon CyLab (FutureEnterprise@CyLab). At ETH Zurich Andrea Lattuada was supported, in part, by a gift from VMware

Goals:
	- https://github.com/verus-lang/verus/wiki/Goals
User doku:
	- https://verus-lang.github.io/verus/guide/
Support für Rust syntax:
	- https://verus-lang.github.io/verus/guide/features.html