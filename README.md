# dependent_data_elimination

# eliminator style

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

