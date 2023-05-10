https://github.com/xldenis/creusot

- So ähnlich wie Prusti, allerdings muss man hier den Verifier (Why3) händisch anwerfen
- Basiert auch auf MIR und RustHorn
- Safe-Rust bisher only
- Für Borrows ganz interessant, kann somit Start und Endwert eines Borrows überprüfen (glaub das kann man mit Prusti old auch machen)
	- @fin: Denotes the value of a brrow when it is dropped
	- @now: Denotes the value of a brrow in the current memory
- Kann auch mit Pre/Postconditions, Invariants etc. umgehen
- https://inria.hal.science/hal-03737878/document