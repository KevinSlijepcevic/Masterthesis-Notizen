- Benötigt Rust Nightly
- Nur Rust Edition 2018 unterstützt lt. prusti-rustc Fehler-Msg (geht komischerweise aber auch mit --edition=2021)
- Integriert sich in den Rustc compiler, bleibt somit immer up-to-date mit der Sprache (prusti-rustc)
- By default: Absence of panics and overflow (kann disabled werden)
- Kann nur partielle Korrektheit beweisen, terminierung wird nicht geprüft
- Ist konservativ, d.h. es werden error reported außer Prusti kann beweisen das diese Eingehalten werden
- #[pure] müssen terminieren, das muss der User sicherstellen


**Doku:**
**Useability:**

