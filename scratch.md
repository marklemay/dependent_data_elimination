### why are only **inaccessable patterns** considered **flexable vars** in Ulf's thesis?
* is it trying to make the interface more predictable to users?
current Agda doesn't seems to require this
```agda
data Copies : ℕ -> Set where
  C0 :  (a : ℕ) -> Copies a
  C2 :  (a : ℕ) -> Copies a -> Copies a -> Copies a

flex : Copies 3 -> Vec Bool 3
flex (C2 x (C0 y) (C0 z)) = replicate {_} {_} {y} true
flex _ = replicate false
```
