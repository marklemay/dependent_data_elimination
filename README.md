# dependent_data_elimination

# Eliminator Style

```
data Nat : * {
  | Z : Nat
  | S : Nat -> Nat
};

data Vec : * -> Nat -> * {
  | Nil  : (A : *) -> Vec A 0
  | Cons : (A : *) -> A -> (n : Nat) -> Vec A n -> Vec A (S n)
};

rep : (A : *) -> A -> (n : Nat) -> Vec A n ;
rep A a n = 
  case n < n' : Nat => Vec A n' >{
   | Z   => Nil A
   | S p => Cons A a p (rep A a p)
   } ;

head : (A : *) -> (n : Nat) -> Vec A (S n) -> A ;
head A n v = 
  case v < _ : Vec A' n' => 
    case n' {
      | Z   => Unit
      | S _ => A'
    }
  >{
   | Nil _        => tt
   | Cons _ a _ _ => a
   } ;
```
more examples in https://github.com/marklemay/dDynamic/blob/master/ex/a.dt

any datype may be eliminated with a `case` that matches every constructor exactly once, and a motive depending on the scrutinee and type params (wither explicit or inferred).

normalization is allowed when the scrutinee has data constructor, https://github.com/marklemay/dDynamic/blob/master/src/Norm.hs#L55-L65.

typing abstractly applies freely constructed data to each branch and then checks against the motive. the type of the overall expression is the term and type of the scrutinee substituted into the mostive, https://github.com/marklemay/dDynamic/blob/master/src/Ty.hs#L60-L117.

## Advantages
* handles all data definition
* simple
  * implementation is straitforward
  * possible to formally prove type soundness
## Disadbantages
* very burecratic, and hard to use

## History
Similar to what Martin lof origionally proposed.  If unrestricted recursion is allowed (as I preffer) the syntax can be further simplified.


# Dependent pattern matching
This I understand less well. Implemented in Agda.

dependent pattern matching is not a straitforward extention of elimination.  There are things that direct elimination can handle that patten matching can not,
```agda
open import Data.Bool
open import Data.Nat -- using (ℕ)
postulate F : Set -> Set

data Sm' : (A : Set) -> Set₁ where
  S' : (A : Set) -> (a : A) -> Sm' (F A)
  N' : (A : Set) -> Sm' A

f' : Sm' ℕ -> Bool
f' (S' _ _) = true
f' (N' _) = false
```
give the unification error:
```
I'm not sure if there should be a case for the constructor S',
because I get stuck when trying to solve the following unification
problems (inferred index ≟ expected index):
  F A ≟ ℕ
when checking that the pattern S' _ _ has type Sm' ℕ
```
this would work in the eliminator style. it apears that the emimination principle can be recovered inderectly
```agda
f'' : (A : Set) -> Sm' A -> Bool
f'' .(F A) (S' A a) = false
f'' A (N' .A) = true

f' : Sm' ℕ -> Bool
f' x = f'' ℕ x
```

## Papers/Notes
* [Coq92](https://wonks.github.io/type-theory-reading-group/papers/proc92-coquand.pdf)
  * is there a final version?
* https://storage.googleapis.com/pub-tools-public-publication-data/pdf/99.pdf
* "view from the left"
  * with abstractions
  * elaborating into eliminators?
* [Epigram](http://www.e-pig.org/)
* Eliminating dependent pattern matching.
* [Towards a practical programming language based on dependent type theory](http://www.cse.chalmers.se/~ulfn/papers/thesis.pdf), chapter 2
  * more of a summery, some of the judgments are missing case, presumably they are availible in the impementation
* [Jesper Cockx thesis](https://jesper.sikanda.be/files/thesis-final-digital.pdf)
* https://jesper.sikanda.be/files/overlapping-and-order-independent-patterns.pdf
* possibly related https://sozeau.gitlabpages.inria.fr/www/research/publications/Equations:_A_Dependent_Pattern-Matching_Compiler.pdf

## Questions
### pattern matching is sensative to the unification algorithm?
* some choices need to be made
  * unique solution vs. satisfiable solution?
  * what are the flexable vars?
  * since it is possible to embed computation in the argument of a type constructor, unification is at worst equivlent to deciding equality so ingeneral undecidable.

### why only allow patterns at definition sites?
* is it only to make termination checking easier?

### why are only **inaccessable patterns** considered **flexable vars** in Agda?
* is it trying to make the interface more predictable to users?

### Is there a way to extend pattern matching so that it can handle all cases the case eliminator would?

### Has anyone workd out the meta theory of deppendnet pattern matching "directly"?
* without, for instance, compiling to eliminators
* was done in https://jesper.sikanda.be/files/overlapping-and-order-independent-patterns.pdf, but with non-standard ordering semantics

