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


Paper:
	- Verification Toolchain based on lightweight functional translation (Abstract)
	- Keine interior mutability or unsafe code (Abstract)
	- New approach für borrows und controlled aliasing: propose a pure, functional semantic for low-level borrow calculus (LLBC)


Youtube: 
	-https://www.youtube.com/watch?v=2sILtqcZqqg
	-Similar to Electrolysis