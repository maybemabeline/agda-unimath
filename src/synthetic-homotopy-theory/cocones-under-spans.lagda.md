# Cocones under spans

```agda
module synthetic-homotopy-theory.cocones-under-spans where
```

<details><summary>Imports</summary>

```agda
open import foundation.action-on-identifications-functions
open import foundation.commuting-squares-of-maps
open import foundation.contractible-types
open import foundation.dependent-pair-types
open import foundation.equality-dependent-pair-types
open import foundation.equivalences
open import foundation.function-extensionality
open import foundation.function-types
open import foundation.functoriality-dependent-pair-types
open import foundation.fundamental-theorem-of-identity-types
open import foundation.homotopies
open import foundation.homotopy-induction
open import foundation.identity-types
open import foundation.structure-identity-principle
open import foundation.universe-levels
open import foundation.whiskering-homotopies
```

</details>

## Idea

A cocone under a span `A <-f- S -g-> B` with vertex `X` consists of two maps
`i : A → X` and `j : B → X` equipped with a homotopy witnessing that the square

```text
      g
  S -----> B
  |        |
 f|        |j
  V        V
  A -----> X
      i
```

commutes.

## Definitions

### Cocones

```agda
cocone :
  {l1 l2 l3 l4 : Level} {S : UU l1} {A : UU l2} {B : UU l3}
  (f : S → A) (g : S → B) → UU l4 → UU (l1 ⊔ l2 ⊔ l3 ⊔ l4)
cocone {A = A} {B = B} f g X =
  Σ (A → X) (λ i → Σ (B → X) (λ j → coherence-square-maps g f j i))

module _
  {l1 l2 l3 l4 : Level} {S : UU l1} {A : UU l2} {B : UU l3} {X : UU l4}
  (f : S → A) (g : S → B) (c : cocone f g X)
  where

  horizontal-map-cocone : A → X
  horizontal-map-cocone = pr1 c

  vertical-map-cocone : B → X
  vertical-map-cocone = pr1 (pr2 c)

  coherence-square-cocone :
    coherence-square-maps g f vertical-map-cocone horizontal-map-cocone
  coherence-square-cocone = pr2 (pr2 c)
```

### Homotopies of cocones

```agda
module _
  {l1 l2 l3 l4 : Level} {S : UU l1} {A : UU l2} {B : UU l3}
  (f : S → A) (g : S → B) {X : UU l4}
  where

  statement-coherence-htpy-cocone :
    (c c' : cocone f g X) →
    (K : (horizontal-map-cocone f g c) ~ (horizontal-map-cocone f g c'))
    (L : (vertical-map-cocone f g c) ~ (vertical-map-cocone f g c')) →
    UU (l1 ⊔ l4)
  statement-coherence-htpy-cocone c c' K L =
    ((coherence-square-cocone f g c) ∙h (L ·r g)) ~
    ((K ·r f) ∙h (coherence-square-cocone f g c'))

  htpy-cocone : (c c' : cocone f g X) → UU (l1 ⊔ l2 ⊔ l3 ⊔ l4)
  htpy-cocone c c' =
    Σ ( horizontal-map-cocone f g c ~ horizontal-map-cocone f g c')
      ( λ K →
        Σ ( vertical-map-cocone f g c ~ vertical-map-cocone f g c')
          ( statement-coherence-htpy-cocone c c' K))

  module _
    (c c' : cocone f g X) (H : htpy-cocone c c')
    where

    horizontal-htpy-cocone :
      horizontal-map-cocone f g c ~ horizontal-map-cocone f g c'
    horizontal-htpy-cocone = pr1 H

    vertical-htpy-cocone :
      vertical-map-cocone f g c ~ vertical-map-cocone f g c'
    vertical-htpy-cocone = pr1 (pr2 H)

    coherence-htpy-cocone :
      statement-coherence-htpy-cocone c c'
        ( horizontal-htpy-cocone)
        ( vertical-htpy-cocone)
    coherence-htpy-cocone = pr2 (pr2 H)

  reflexive-htpy-cocone :
    (c : cocone f g X) → htpy-cocone c c
  pr1 (reflexive-htpy-cocone (i , j , H)) = refl-htpy
  pr1 (pr2 (reflexive-htpy-cocone (i , j , H))) = refl-htpy
  pr2 (pr2 (reflexive-htpy-cocone (i , j , H))) = right-unit-htpy

  htpy-eq-cocone :
    (c c' : cocone f g X) → c ＝ c' → htpy-cocone c c'
  htpy-eq-cocone c .c refl = reflexive-htpy-cocone c

  is-contr-total-htpy-cocone :
    (c : cocone f g X) → is-contr (Σ (cocone f g X) (htpy-cocone c))
  is-contr-total-htpy-cocone c =
    is-contr-total-Eq-structure
      ( λ i' jH' K →
        Σ ( vertical-map-cocone f g c ~ (pr1 jH'))
          ( statement-coherence-htpy-cocone c (i' , jH') K))
      ( is-contr-total-htpy (horizontal-map-cocone f g c))
      ( horizontal-map-cocone f g c , refl-htpy)
      ( is-contr-total-Eq-structure
        ( λ j' H' →
          statement-coherence-htpy-cocone c
            ( horizontal-map-cocone f g c , j' , H')
            ( refl-htpy))
        ( is-contr-total-htpy (vertical-map-cocone f g c))
        ( vertical-map-cocone f g c , refl-htpy)
        ( is-contr-is-equiv'
          ( Σ ( ( horizontal-map-cocone f g c ∘ f) ~
                ( vertical-map-cocone f g c ∘ g))
              ( λ H' → coherence-square-cocone f g c ~ H'))
          ( tot (λ H' M → right-unit-htpy ∙h M))
          ( is-equiv-tot-is-fiberwise-equiv (λ H' → is-equiv-concat-htpy _ _))
          ( is-contr-total-htpy (coherence-square-cocone f g c))))

  is-equiv-htpy-eq-cocone :
    (c c' : cocone f g X) → is-equiv (htpy-eq-cocone c c')
  is-equiv-htpy-eq-cocone c =
    fundamental-theorem-id
      ( is-contr-total-htpy-cocone c)
      ( htpy-eq-cocone c)

  extensionality-cocone :
    (c c' : cocone f g X) → (c ＝ c') ≃ htpy-cocone c c'
  pr1 (extensionality-cocone c c') = htpy-eq-cocone c c'
  pr2 (extensionality-cocone c c') = is-equiv-htpy-eq-cocone c c'

  eq-htpy-cocone :
    (c c' : cocone f g X) → htpy-cocone c c' → c ＝ c'
  eq-htpy-cocone c c' = map-inv-is-equiv (is-equiv-htpy-eq-cocone c c')
```

### Postcomposing cocones

```agda
cocone-map :
  {l1 l2 l3 l4 l5 : Level} {S : UU l1} {A : UU l2} {B : UU l3}
  (f : S → A) (g : S → B) {X : UU l4} {Y : UU l5} →
  cocone f g X → (X → Y) → cocone f g Y
pr1 (cocone-map f g c h) = h ∘ horizontal-map-cocone f g c
pr1 (pr2 (cocone-map f g c h)) = h ∘ vertical-map-cocone f g c
pr2 (pr2 (cocone-map f g c h)) = h ·l coherence-square-cocone f g c

cocone-map-id :
  {l1 l2 l3 l4 : Level} {S : UU l1} {A : UU l2} {B : UU l3}
  (f : S → A) (g : S → B) {X : UU l4} (c : cocone f g X) →
  Id (cocone-map f g c id) c
cocone-map-id f g c =
  eq-pair-Σ refl
    ( eq-pair-Σ refl (eq-htpy (ap-id ∘ coherence-square-cocone f g c)))

cocone-map-comp :
  {l1 l2 l3 l4 l5 l6 : Level} {S : UU l1} {A : UU l2} {B : UU l3}
  (f : S → A) (g : S → B) {X : UU l4} (c : cocone f g X)
  {Y : UU l5} (h : X → Y) {Z : UU l6} (k : Y → Z) →
  Id (cocone-map f g c (k ∘ h)) (cocone-map f g (cocone-map f g c h) k)
cocone-map-comp f g (i , j , H) h k =
  eq-pair-Σ refl (eq-pair-Σ refl (eq-htpy (ap-comp k h ∘ H)))
```

### Horizontal composition of cocones

```text
      i       k
  A ----> B ----> C
  |       |       |
 f|       |       |
  v       v       v
  X ----> Y ----> Z
```

```agda
cocone-comp-horizontal :
  { l1 l2 l3 l4 l5 l6 : Level}
  { A : UU l1} {B : UU l2} {C : UU l3} {X : UU l4} {Y : UU l5} {Z : UU l6}
  ( f : A → X) (i : A → B) (k : B → C) ( c : cocone f i Y) →
  cocone (vertical-map-cocone f i c) k Z → cocone f (k ∘ i) Z
pr1 (cocone-comp-horizontal f i k c d) =
  ( horizontal-map-cocone (vertical-map-cocone f i c) k d) ∘
  ( horizontal-map-cocone f i c)
pr1 (pr2 (cocone-comp-horizontal f i k c d)) =
  vertical-map-cocone (vertical-map-cocone f i c) k d
pr2 (pr2 (cocone-comp-horizontal f i k c d)) =
  pasting-horizontal-coherence-square-maps
    ( i)
    ( k)
    ( f)
    ( vertical-map-cocone f i c)
    ( vertical-map-cocone (vertical-map-cocone f i c) k d)
    ( horizontal-map-cocone f i c)
    ( horizontal-map-cocone (vertical-map-cocone f i c) k d)
    ( coherence-square-cocone f i c)
    ( coherence-square-cocone (vertical-map-cocone f i c) k d)
```

### Vertical composition of cocones

```text
     i
 A -----> X
 |        |
f|        |
 v        v
 B -----> Y
 |        |
k|        |
 v        v
 C -----> Z
```

```agda
cocone-comp-vertical :
  { l1 l2 l3 l4 l5 l6 : Level}
  { A : UU l1} {B : UU l2} {C : UU l3} {X : UU l4} {Y : UU l5} {Z : UU l6}
  ( f : A → B) (i : A → X) (k : B → C) ( c : cocone f i Y) →
  cocone k (horizontal-map-cocone f i c) Z → cocone (k ∘ f) i Z
pr1 (cocone-comp-vertical f i k c d) =
  horizontal-map-cocone k (horizontal-map-cocone f i c) d
pr1 (pr2 (cocone-comp-vertical f i k c d)) =
  vertical-map-cocone k (horizontal-map-cocone f i c) d ∘
  vertical-map-cocone f i c
pr2 (pr2 (cocone-comp-vertical f i k c d)) =
  pasting-vertical-coherence-square-maps
    ( i)
    ( f)
    ( vertical-map-cocone f i c)
    ( horizontal-map-cocone f i c)
    ( k)
    ( vertical-map-cocone k (horizontal-map-cocone f i c) d)
    ( horizontal-map-cocone k (horizontal-map-cocone f i c) d)
    ( coherence-square-cocone f i c)
    ( coherence-square-cocone k (horizontal-map-cocone f i c) d)
```
