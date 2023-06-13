https://github.com/rust-lang/miri
- Basiert auf MIR
- Benötigt Rust Nightly
- Ist ein Interpreter, welchen unsicheren Rust-Code ausführt (vgl. MiniRust) und Code auf Undefined Behaviour prüft (uninitilized Memory, out of bounds memory acces, memory leaks,  etc.), kann inzwischen auch Concurrency Bugs finden (=Data race)
- Unter dem Hut der Rust Foundation
- Ist eine Virtual Machine für Rust
- Limitations
	- Slow
	- Can only detect when UB happens **(also ist das Dynamic Analysis eig?)**
	- Can not detect data races
	- Only interpret Rust Code (kein C-Bindung o.ä.) -> Kann aber foreign functions emulieren, müssen wie bei Prusti vorgefertigt sein (von Community z.B.), genannt *shims*
- Kann cross-plattform prüfen zB. big-endian auf little-endian devices oder 64-bit macos auf 32-bit linux etc.
- Hat schon einen haufen Bugs gefunden
- Ist gut um zB Tests auszuführen und so UB zu finden, welches ansonsten manchmal gut gehen kann, manchmal nicht
- Operationen sind teils random, Miri geht also einen Tag durch und failed den anderen Tag (nur bei Concurrency, da PRNG benutzt wird (Preempting z.B))
- Insgesamt aber sehr mächtiges Tool und sollte in jeder CI integriert werden

Borrow-Stack (Konzeptionelles Model für raw pointer, https://rust-unofficial.github.io/too-many-lists/fifth-stacked-borrows.html) ist interessant, arbeitet wie ein Stack und nur der oberste pointer ist valide, wenn man einen darunter liegenden pointer benutzen möchte, müssen erst die darüber liegenden gepopt werden. Miri checkt das v.a. im strict Mode:  ```MIRIFLAGS="-Zmiri-tag-raw-pointers"```

Neuer Vortrag 2023:
- https://www.youtube.com/watch?v=svR0p6fSUYY