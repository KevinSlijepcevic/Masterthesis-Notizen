https://github.com/xldenis/creusot

- So ähnlich wie Prusti, allerdings muss man hier den Verifier (Why3) händisch anwerfen
- Basiert auch auf MIR und RustHorn
- Safe-Rust bisher only
- Für Borrows ganz interessant, kann somit Start und Endwert eines Borrows überprüfen (glaub das kann man mit Prusti old auch machen)
	- @fin: Denotes the value of a brrow when it is dropped
	- @now: Denotes the value of a brrow in the current memory
- Kann auch mit Pre/Postconditions, Invariants etc. umgehen
- https://inria.hal.science/hal-03737878/document
- 2 Papers
- RustHornBelt verlinken, ist Idee um unsafe rust code zu prüfen, muss aber in Creusot dann eingekippt werden
	- https://dl.acm.org/doi/pdf/10.1145/3519939.3523704
	- https://www.youtube.com/watch?v=pOg4dEhr5hI
	- https://github.com/xldenis/rhb-specs
	- Bisher aber eher konzept, deshalb erwähnen aber nicht als eigenständiges tool aufnehmen?
	- Ist "nur" eine Semantic Foundation, keine fertige Implementierung
	- **Wird aber bereits im original Paper erwähnt, also eher nicht mit aufnehmen**


Paper:
	- Testsuite vorhanden ala Prusti mit praktischen Beispielen
	- CreuSAT wurde damit verifiziert
	- Evtl Kapitel 6 im Bezug auf Prusti zusammenfassen und auch auf Aenas

Paper 2 (ist die detailiertere aber ältere variante):
	-	Based on RustHorn

Install:
	- brew expat gtk+3 gtksourceview3 libxml2 autoconf
	- ~/.opam
	- `opam install lablgtk3 lablgtk3-sourceview3 ocamlgraph why3 why3-ide`

Usage:
	- In rustup die exakte Version setzen, mit der das Tool auch compiliert wurde, sonst fehler, nightly-2023-05-26-aarch64-apple-darwin