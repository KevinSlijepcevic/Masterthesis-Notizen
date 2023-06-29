- Benötigt Rust Nightly
- Ist ein cargo pkg (Prüfen, ob es in Rustc integriert ist)
- Checkt panic-Freedom und data flow reachability analysis
- Es existiert ein crate für annotations (mit Pre/Postconditions etc., aber keine Loop-Invariants)
- Dokumentation sehr dürftig

Wenn Analysis einen panic-Path findet, versucht dieser eine Precondition zu erstellen, das diese Panic nicht mehr erreicht wird.
Wenn es keinen MIR gibt (z.B. Calls einer externen Lib oder C-Code call), kann MIRAI keine Panic-Freedom checken. MIRAI included dafür Summaries für viele Libs, Anwender können dafür auch eine Summary anlegen in Ihren Code.
Falls es keine Summary gibt, leitet MIRAI eine Precondition her, die dafür sorgt das der Call nicht ausgeführt wird (Nutzen?). 

https://github.com/facebookexperimental/MIRAI/tree/main/annotations

**Dokumentation:**
- Overview https://github.com/facebookexperimental/MIRAI/blob/main/documentation/Overview.md:
	- MIRAI wurde aufgrund von [Diem](https://github.com/diem/diem) entwickelt, da Rust als Programmiersprache ausgewählt wurde aber es keine static analysis tool gab für panic freedom
	- Macht eine reachability analysis: Ab Eintrittspunkt, werden alle möglichen Programmflusspfade abgedeckt und geprüft, ob ein Pfad eine abrupt runtime termination auslösen kann; Idealerweise kann mathematisch angenommen werden, wenn MIRAI keinen solchen Pfad findet das kein solcher Pfad exisitiert; in practice this is not always possible automatically, es gibt also false positives und false negatives
	- Wieso nicht die anderen Tools? Sind eher research projects; By contrast, the goal of MIRAI is to produce a Rust compiler plug-in that fits smoothly into cargo, can ingest arbitrary, unannotated rust code, produce actionable diagnostics with a low false positive rate, and do this so quickly and efficiently that it can be part of every Rust developer's normal work flow, as well as part of a continuous integration work flow. If this can be done without having to come up with novel solutions to difficult problems, so much the better. This [paper](https://alastairreid.github.io/papers/hatra2020.pdf) is an excellent exposition of the ambition that the MIRAI project is inspired by and the problem space that it finds itself in.
	- MIRAI v1.1.* is a stable release and is expected to work in most cases. Any issues reported on Github will be investigated and fixed as appropriate. There are currently no plans for a v2. --> Also schon richtung feature complete?
	- **Entry points:** MIRAI muss mit einem entry point anfangen, dieser muss externally callbar sein z.B. main oder pub func of crate, or a unit test; es gehen keine generic oder higher order functions; das kann bei einer library crate bedeuten, das nur die unit tests eig als entry point genutzt werden, ++ für unit tests
	- **False positives** können z.B. wenn fkt einen codebranch enthält z.b. für none der einen panic auslöst, der dev aber davon ausgeht das die fkt eig immer some erhält, woher soll MIRAI das wissen -> MIRAI kann mit expliziten checks am anfang der fkt umgehen und somit sowas vermeiden
		- MIRAI nimmt alle code paths die zum abbruch führen bei einem input-parameter als gewollte checks auf und fügt preconditions für diese funktion dann ein, dann werden call sites gechecked das diese preconditions eingehalten werden
	- **Incomplete Analysis**: Wenn keine MIR vorhanden ist (z.B. std library, foreign function), können false positives and false negatives auftauchen da MIRAI diese fkts nicht ordentlich prüfen kann, Incomplete kann auch bei timeouts auftauchen
		- Diese funktionen werden als unreachable markiert, um eine flut an false positives zu vermeiden, mit `--diag=verify` wird eine diagnostik dazu erstellt wenn verification gewünscht ist
	- **Library Code**: Best practice: libraries mit `--diag=library` prüfen, any path that panics will generate a diagnostic unless the path is precluded by an explicit precondition
		- MIRAI unterstützt 2 crates für annotations: [design by contracts](https://crates.io/crates/contracts) und [mirai annotations crate](https://crates.io/crates/mirai-annotations) wobei MIRAI is more full featured and also the only way to access more advanced annotations, such as tagging for taint analysis
	- **Invariants**:
		 - design by contracts crate unterstützt das für struct fields, estabilished by constructors, maintainted by mutators and assumed by readers
		 - MIRAI annotations unterstützt das nicht, das wird assumed via explicit preconditions and established via explicit postconditions
		 - Future work erweitert evtl MIRAI annotations für invariants
	- **Quantifiers**:
		- kein support, war bisher aber auch kein hindernis
	- **Taint analysis**:
		- MIRAI annotations benötigt um taint analysis zu aktivieren
		- [examples and doku](https://github.com/facebookexperimental/MIRAI/blob/main/documentation/TagAnalysis.md)
		- Idea: Tag a source of untrusted data, sinks that need to trust data need preconditions that check for the absence of untrusted tags
		- MIRAI special: Tags can be added but not removed, tags can propagate, e.g. if value x has a tag that propagates via addition operations, then value x + 1 will also have that tag
	- **Constant time analysis**: 
		- Specialzed form of tainted analysis: Constant time analysis is a specialized form of taint analysis. It determines whether a sensitive bit of data, such as a password or private encryption key, can influence the running time of the program (and thus leak the sensitive data via a side-channel). Value has to be tagged which may influence time
- Abstract Interpretation (Technik Kapitel): https://github.com/facebookexperimental/MIRAI/blob/main/documentation/Overview.md
	- **Interpreter**: MIRAI is built as an interpreter that can deal with both concrete and abstract values
	- **Abstract Values**: The values that MIRAI operates on can be thought of as symbolic expressions from which classical abstract domain values are computed on demand, so it is also fair to think of MIRAI as a symbolic execution engine.
	- **Environment**: ??
	- **Summaries**: ??
	- **Call resolution**: ??
	- **Foreign Functions**: Kein MIR verfügbar, z.B. System calls oder calls in andere Sprache oder Std calls ohne #[inline] attribute; solche Funktionen benötigen ein "model" um calls zu diesen zu analysieren, MIRAI liefert diese mit für viele of the standard library foriegn functions
	- **Path conditions**:  ???
	- Loops und recursion anscheinend nicht unterstützt (bisher)
	- **k-limit**: Einstellbar wie tief symbolic expressions werden dürfen, erwähnt auch Z3 solver query timeout, also Z3 als SMT solver dahinter?
	- **Future Work**: Better support for parameterized unit tests. Providing a counter example when failing to prove that a panic is not reachable. Github actions to make it dead simple to run MIRAI over your Pull Requests. Human readable function summaries. IDE integration for better code navigation and debugging support (i.e. how did control get here?)

Abstract Values https://github.com/facebookexperimental/MIRAI/blob/main/documentation/AbstractValues.md:
- So, from now on we think of an abstract value as a set of concrete values that contains all of the concrete values that can reach the (program) fragment (=path), as well as some values that actually will never reach it. That is where false positives come from.
- ??

Architecture https://github.com/facebookexperimental/MIRAI/blob/main/documentation/Architecture.md:
- Rust compiler plguin für MIR und https://github.com/facebookexperimental/MIRAI/blob/main/documentation/WhyPlugIn.md

Caching https://github.com/facebookexperimental/MIRAI/blob/main/documentation/Caching.md:
- Instead, when MIRAI runs over a package target, it does a top-down analysis and computes summaries on demand whenever it encounters a call site where an un-summarized function is called. During summarization, the actual argument types, generic arguments and function constant arguments of the call site are used during the analysis and hence the resulting summary is cached with a key that incorporates these elements.
- Also macht die Analyse nicht für jeden Funktion call von neu (nicht persistent aber!)

Call Graph Generator https://github.com/facebookexperimental/MIRAI/blob/main/documentation/CallGraph.md:
- After the target program is statically analyzed, MIRAI supports output of the generated call graph in two output formats:
	- `dot`: for use with Graphviz to create a visualization of the call graph.
	- `ddlog`: the call graph in the form of Datalog input relations.

Incremental Analysis https://github.com/facebookexperimental/MIRAI/blob/main/documentation/IncrementalAnalysis.md:
- Ideally, Mirai should just become a regular part of the Rust Language Service (RLS) and provide diagnostics while programs are being edited.
If this is to happen, Mirai should be fast, which is not something one typically associates with program verification tools. Furthermore, as discussed here, parallelism is not going to help.

Tag Analysis https://github.com/facebookexperimental/MIRAI/blob/main/documentation/TagAnalysis.md:
- Taint Analysis is special form of Tag Analysis
- Unterstützt: context-, field-, flow-, and path-sensitive tag analysis
- Löst mit Hilfe von Z3 diese, wenn data-flow-analysis is not precise enough
- MIRAI avoided false negatives, also eher konservativ; hier auch