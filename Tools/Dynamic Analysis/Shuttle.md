- Concurrency checker crate
- https://crates.io/crates/shuttle
- Basiert auf Zufall (mit probabilistischen Garantien)
- Von Microsoft Research bzw. AWS
- https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/asplos277-pct.pdf
- https://github.com/awslabs/shuttle
- Führt Tests mit random Parametern für Scheduler aus, um so Seiteneffekte zu finden
- Ist KEIN exhaustive Checker so wie Loom, wurde aber von Loom inspiriert