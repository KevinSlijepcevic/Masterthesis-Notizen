**Begriffe Verifikation**

Quantified Permissions:
-  forall/exists: Quantoren

Magic Wand:
- "H1 wand H2", defines a heap predicate such that, if we extend it with H1, we obtain H2

Symbolic Execution:
- Es werden nicht alle Werte durchgetestet, sondern symbolisch betrachtet

Hoare-Logic:
- Precondition -> Fkt. -> Postcondition

Seperation Logic:
- Basiert auf Hoare-Triple, benutzt *store (stack)* und *heap* 
- Frame Logic: Wenn ein Programm sicher in einem Bereich ausgeführt wird, kann dieses auch in einem größeren Bereich ausgeführt werden, ohne das andere Speicherbereiche verändert werden, die nicht von dem Programm verändert werden sollen
- Fractional Ressources: Man kann auf einen Speicherbereich read-write oder nur read permssions geben
- Concurrent Seperation Logic: Wird zum Beweisen von Threads benutzt, welche getrennte Speicherbereiche aufrufen

Linear Types: 
- Corresponds to linear logic and ensures that objects are used exactly once

![[Pasted image 20230506163519.png]]
https://www.youtube.com/watch?v=vE5Jwf_Lx94