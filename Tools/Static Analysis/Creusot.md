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
	- Kann mit Traits umgehen, kann kein unsafe aber kann mit safe abstractions like Vec umgehen
	- Sprache für Specifications is called Pearlite (Pre/Post, Implications, Quantifiers (forall, exisits) unterstützt, Loop Invariants, Logical equality = = = , Result keyword)
	- @: Model Operator for Model Trait
	- ^: Final operator for Prophecy, für access of mutable borrow at end of lifetime, * current val, wird so von Why3 
	- Unterstützt ghost types
	- Annotated programm is getting translated into Why3, Why3 unterstützt verschiedene SMT Solver, pluspunkt da Why3 gut getestet und weit verbreitet ist
	- Pearlite formulas are type checked, not borrow checked
	- User can introduce user lemmas
	- Creusot translated programms into WhyML, the programming language of Why3
	- Benutzt MIR -> Benutzt Why3 input front-end MLCFG welcher das WhyML erstellt
	- Benutzt traits aktiv zum verifiying
	- Trait result?? Trait Model?? -> Evtl. mal Testbeispiele prüfen für Verständnis
	- Extends Rust compiler
	- Vorteil der klapseung in pearlite makro ist das die funtkionen nicht im normalen programmablauf verwendet werden können, wie z.b. pure functions in prusti
	- Evtl Kapitel 6 im Bezug auf Prusti zusammenfassen und auch auf Aenas
	- Creusot verlässt sich auf Rusts type system, nicht wie bei prusti

Paper 2 (ist die detailiertere aber ältere variante):
	-	Based on RustHorn