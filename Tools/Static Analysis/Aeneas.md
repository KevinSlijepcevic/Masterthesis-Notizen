- https://github.com/AeneasVerif/aeneas
- Wandelt Rust Programme in pure lambda calculus um 
- Für Safe Rust
- Verwendet keine Annotations
-  Bildet für jede Funktion eine forward und eine backward implementation in funktional
- Bisher wird F* und Coq SMT Solver unterstützt, sollen aber auch andere unterstützt werden z.B. Isabelle
- (Bisher) nur safe Rust, keine Traits, keine Loops, keine interior mutability, no concurrent exec
- Ist nicht automatisiert, man gibt Aeneas ein Rust-Programm und es bildet ein pure model daraus und dann kann man machen was man will damit im Solver (dort müssen dann auch die Proofs geschrieben werden)  
- Keine nested-borrows bisher
- Relativ aktiv

Youtube: 
	-https://www.youtube.com/watch?v=2sILtqcZqqg
	-Similar to Electrolysis
	- Bildet ein pure model aus non-annotated Rust Code, für z.B. Coq, Isabelle oder HOL4, wo dann der User write and prove lemmas machen kann
	- Es gibt keine borrows in der pure world
	- Bildet für jede Funktion eine forward und eine backward implementation in funktional, für jede Rust funktion, back ist benötigt für propagating changes back into the environment
		- Forward: Nehme normalen Rust Rumpf einer Funktion und wandle diese in funktionale Parameter um, z.B. Borrow entfernen
		- Backward: Nehme den normalen Rust Rumpf und füge als Eingabeparameter noch den mutable Return-Wert Funktion ein
		- Basiert auf MIR
		- MIR wird von Charon (Rust compiler plugin) prozessiert, returned ein Low-Level Borrow Calculus (LLBC)
		- LLBC wird in Aenas (OCaml Programm) und entwirft daraus das Pure Model
			- Translation basiert auf symbolic execution
			-Vertraut nicht dem borrow checker! Erstellt einen eigenen Borrow Graph
- No traits, no loops, no interior mutability

Paper (long version, 2022):
	- Verification Toolchain based on lightweight functional translation (Abstract)
	- Keine interior mutability or unsafe code (Abstract)
	- Verwendet Rust memory safety guarantees, um den Dev zu ermöglichen sich auf funktionale Parameter für Verifikation zu konzentrieren (Abstract)
	- New approach für borrows und controlled aliasing: propose a pure, functional semantic for low-level borrow calculus (LLBC) (Abstract)
- Approximation of borrow graph in presense of function calls, erlaubt backward functions (Abstract)
- **Introduction**
	- Our translation handles shared, mutable, two-phase and re-borrow
	- We wish to emphasize that our functional translation is completely generic, One could easily add additional backends (Coq, Lean, Viper, Why)
	- Focus on Rust subset that is functional in essence
	- "Map variables to values and track aliasing in a very fine-grained manner"
	- Verwendet die essence of borrowing, also keine lifetime parameter per se
	- Currently F* und Coq (prüfen, ob bereits support fertig)
	- Evaluation with resizeable hash table
	- Vorteile: 
		- Verification engineer deals with intrinsic difficulty of the proofs, rather than the incidental complextiy, can also also auf die wichtigen Sachen konzentrieren ohne das außen rum zu betrachten
		- Lightweight, ohne annotations
		- Nicht fixitiert auf ein Verification Framework, Engineer kann sich aussuchen in dem sie am produktivsten sind
	- In short, Aeneas reveals the functional essence of Rust programs, and verifies them as such
- **Kap.2**
	- Increase Beispiel wird gleich in overflow-checked pure function umgewandelt
	- Wenn Rust fkt () returned dann gibt es nur forward function, bei mutable return gibt es backwards und forwards
	- The intuitive effect of calling choose_back is as follows: we relinquish z, which was in region ’a; doing so, we propagate any updates that may have been performed through z onto the variables whose ownership was transferred to ’a in the first place, namely x and y. 
- **Kap.3**
	- Semantic einführung von mut/immut borrows
	- Benutzt alias tracking um den borrow checker nicht vertrauen zu müssen
	- Beispiele für Mutable borrows, shared borrows und reborrows
		- Reborrows get reorganized lazy, and dont terminate until we need to get the borrowed value back
- Kap.3.1
	- LLBC is in large part inspired by MIR, LLBC retains some high-level-constructs: control-flow remains structured, pattern matching und pure expressions to allocate data types, we see strctures as data types equipped with a single constructor
- Kap.4
	- Rekursion wird unterstützt
	- Zusammenfassung: Concrete -> Symbolic Semantic (LLBC) -> Functional Code
	- Translation of forward function is carried out by performing a symbolic execution, synthesizing an AST (also function ohne mutable return)
	- Translation of backward function: mutable parameter als input, mutable parameter als output
- Kap 5:
	- Implementation written in Rust and Ocaml
	- Charon: Rusts MIR -> LLBC AST; Rust compiler plugin
	- Charon lebt als externes Projekt
	- Aenas nimmt LLBC AST und transformiert in funktionales Model, currently to F*
	- Coq wird aktiv gearbeitet, HOL4 und Lean sind planed
	- Unterstützung: General borrows, return borrows, keine loops, keine closures/traits, termination-check möglich, can reason about external life like I/O möglich, framework doesnt trust rust borrow checker, erlaubt extrensic proofs, framework creates executable translation of Rust programm
	- Invariants prüfen die übersetzung, sehr tight
	- No annotations nötig in Rust, Aenas fügt für F* und Coq aber eigenmächtig welche hinzu:
		- Für F*: decreases annotation, which refers to a yet-to-be-defined user-lemma, welches user noch selber anfertigen muss
		- Für Coq: We intent to rely on a fuel parameter controlled by the user in a similar fashion
	- für interacting with external world und external libraries: mark some modues in a given crate as "opaque", die entsprechenden Funktionen erscheinen als ein Interface, meaning they are assumed. the user can then provide a hand-maintained library of lemmas that characterize the behaviour of these external functions
	- Finally, we provide support for enhancing our working monad with an external world. That is, rather than working in the error monad, we generate code for a combined world and error monad. Combined with the opaque feature, this allows the user to modularly state their assumptions about the world, and gives us in practice a lightweight monadic effect system.
Kap 6. Evaluation with HashTable: Functions: insert, get, get_mut und remove
Kap. 7: 
- Loops sollen bald unterstützt werden, Ziel: feature paritiy with Creusot and Prusti
- Danach Traits, Coq Backend


