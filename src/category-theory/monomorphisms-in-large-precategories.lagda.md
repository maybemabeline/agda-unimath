# Monomorphisms in large precategories

```agda
module category-theory.monomorphisms-in-large-precategories where
```

<details><summary>Imports</summary>

```agda
open import category-theory.isomorphisms-in-large-precategories
open import category-theory.large-precategories

open import foundation.action-on-identifications-functions
open import foundation.embeddings
open import foundation.equivalences
open import foundation.identity-types
open import foundation.propositions
open import foundation.universe-levels
```

</details>

## Idea

A morphism `f : x → y` is a monomorphism if whenever we have two morphisms
`g h : w → x` such that `f ∘ g = f ∘ h`, then in fact `g = h`. The way to state
this in Homotopy Type Theory is to say that postcomposition by `f` is an
embedding.

## Definition

```agda
module _
  {α : Level → Level} {β : Level → Level → Level}
  (C : Large-Precategory α β) {l1 l2 : Level} (l3 : Level)
  (X : obj-Large-Precategory C l1) (Y : obj-Large-Precategory C l2)
  (f : hom-Large-Precategory C X Y)
  where

  is-mono-prop-Large-Precategory : Prop (α l3 ⊔ β l3 l1 ⊔ β l3 l2)
  is-mono-prop-Large-Precategory =
    Π-Prop
      ( obj-Large-Precategory C l3)
      ( λ Z → is-emb-Prop (comp-hom-Large-Precategory C {X = Z} f))

  is-mono-Large-Precategory : UU (α l3 ⊔ β l3 l1 ⊔ β l3 l2)
  is-mono-Large-Precategory = type-Prop is-mono-prop-Large-Precategory

  is-prop-is-mono-Large-Precategory : is-prop is-mono-Large-Precategory
  is-prop-is-mono-Large-Precategory =
    is-prop-type-Prop is-mono-prop-Large-Precategory
```

## Properties

### Isomorphisms are monomorphisms

```agda
module _
  {α : Level → Level} {β : Level → Level → Level}
  (C : Large-Precategory α β) {l1 l2 : Level} (l3 : Level)
  (X : obj-Large-Precategory C l1) (Y : obj-Large-Precategory C l2)
  (f : iso-Large-Precategory C X Y)
  where

  is-mono-iso-Large-Precategory :
    is-mono-Large-Precategory C l3 X Y (hom-iso-Large-Precategory C f)
  is-mono-iso-Large-Precategory Z g h =
    is-equiv-is-invertible
      ( λ P →
        ( inv
          ( left-unit-law-comp-hom-Large-Precategory C g)) ∙
          ( ( ap
            ( λ h' → comp-hom-Large-Precategory C h' g)
            ( inv (is-retraction-hom-inv-iso-Large-Precategory C f))) ∙
            ( ( associative-comp-hom-Large-Precategory C
              ( hom-inv-iso-Large-Precategory C f)
              ( hom-iso-Large-Precategory C f)
              ( g)) ∙
              ( ( ap
                ( comp-hom-Large-Precategory C
                  ( hom-inv-iso-Large-Precategory C f))
                ( P)) ∙
                ( ( inv
                  ( associative-comp-hom-Large-Precategory C
                    ( hom-inv-iso-Large-Precategory C f)
                    ( hom-iso-Large-Precategory C f)
                    ( h))) ∙
                  ( ( ap
                    ( λ h' → comp-hom-Large-Precategory C h' h)
                    ( is-retraction-hom-inv-iso-Large-Precategory C f)) ∙
                    ( left-unit-law-comp-hom-Large-Precategory C h)))))))
      ( λ p →
        eq-is-prop
          ( is-set-hom-Large-Precategory C Z Y
            ( comp-hom-Large-Precategory C
              ( hom-iso-Large-Precategory C f)
              ( g))
            ( comp-hom-Large-Precategory C
              ( hom-iso-Large-Precategory C f)
              ( h))))
      ( λ p → eq-is-prop (is-set-hom-Large-Precategory C Z X g h))
```
