What makes haslkel style pattern matching good?
* parrerns are an exact subset of terms, and an objecrt can be reconstituted from it's pattern
* works well with ML style type inference
* coverage checking is relatively striaght forward
* encourages structurally safe recursions

Even then constructor only pattern matching can be inconvenient
* Scala genralizes pattern matching with https://docs.scala-lang.org/tour/extractor-objects.html
  * it is suprizing encapulation like this isn't supported in Haskell for instance
  * no longer enforces pattern term symetry
* OCaml, rust allow "ranges" and alternatives to be matched
  * why not allow mutliple conditions to flow togeether, i.e. non-breaking switch statments?
  * ranges can still allow for objects to be reconstituted with @, of couse that would justify anything
* view pattern extention haskell
* with monads in haskell
