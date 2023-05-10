- Benötigt Rust Nightly
- Ist ein cargo pkg (Prüfen, ob es in Rustc integriert ist)
- Checkt panic-Freedom und data flow reachability analysis
- Es existiert ein crate für annotations (mit Pre/Postconditions etc., aber keine Loop-Invariants)
- Dokumentation sehr dürftig

Wenn Analysis einen panic-Path findet, versucht dieser eine Precondition zu erstellen, das diese Panic nicht mehr erreicht wird.
Wenn es keinen MIR gibt (z.B. Calls einer externen Lib oder C-Code call), kann MIRAI keine Panic-Freedom checken. MIRAI included dafür Summaries für viele Libs, Anwender können dafür auch eine Summary anlegen in Ihren Code.
Falls es keine Summary gibt, leitet MIRAI eine Precondition her, die dafür sorgt das der Call nicht ausgeführt wird (Nutzen?). 