- Alternativer Borrow-Checker
- Ist bereits als experimental in nightly rustc dabei
- Prüft die Origin von einem Loan, anstatt der Lifetime und leitet daraus die Liveness her, also ob ein Loan verwendet wird
- **Nicht sicher ob das zu Verifikation gehört**