# Faithful maps between precategories

```agda
module category-theory.faithful-maps-precategories where
```

<details><summary>Imports</summary>

```agda
open import category-theory.functors-precategories
open import category-theory.maps-precategories
open import category-theory.precategories

open import foundation.dependent-pair-types
open import foundation.embeddings
open import foundation.equivalences
open import foundation.injective-maps
open import foundation.propositions
open import foundation.universe-levels
```

</details>

## Idea

A [map](category-theory.maps-precategories.md) between
[precategories](category-theory.precategories.md) `C` and `D` is **faithful** if
its an [embedding](foundation-core.embeddings.md) on hom-sets.

Note that embeddings on [sets](foundation-core.sets.md) happen to coincide with
[injections](foundation.injective-maps.md), but we define it in terms of the
stronger notion, as this is the notion that generalizes.

## Definition

### The predicate of being faithful on maps between precategories

```agda
module _
  {l1 l2 l3 l4 : Level}
  (C : Precategory l1 l2)
  (D : Precategory l3 l4)
  (F : map-Precategory C D)
  where

  is-faithful-map-Precategory : UU (l1 ⊔ l2 ⊔ l4)
  is-faithful-map-Precategory =
    (x y : obj-Precategory C) → is-emb (hom-map-Precategory C D F {x} {y})

  is-prop-is-faithful-map-Precategory : is-prop is-faithful-map-Precategory
  is-prop-is-faithful-map-Precategory =
    is-prop-Π² (λ x y → is-prop-is-emb (hom-map-Precategory C D F {x} {y}))

  is-faithful-prop-map-Precategory : Prop (l1 ⊔ l2 ⊔ l4)
  pr1 is-faithful-prop-map-Precategory = is-faithful-map-Precategory
  pr2 is-faithful-prop-map-Precategory = is-prop-is-faithful-map-Precategory
```

### The predicate of being injective on hom-sets on maps between precategories

```agda
module _
  {l1 l2 l3 l4 : Level}
  (C : Precategory l1 l2)
  (D : Precategory l3 l4)
  (F : map-Precategory C D)
  where

  is-injective-hom-map-Precategory : UU (l1 ⊔ l2 ⊔ l4)
  is-injective-hom-map-Precategory =
    (x y : obj-Precategory C) → is-injective (hom-map-Precategory C D F {x} {y})

  is-prop-is-injective-hom-map-Precategory :
    is-prop is-injective-hom-map-Precategory
  is-prop-is-injective-hom-map-Precategory =
    is-prop-Π²
      ( λ x y →
        is-prop-is-injective
          ( is-set-hom-Precategory C x y)
          ( hom-map-Precategory C D F {x} {y}))

  is-injective-hom-prop-map-Precategory : Prop (l1 ⊔ l2 ⊔ l4)
  pr1 is-injective-hom-prop-map-Precategory = is-injective-hom-map-Precategory
  pr2 is-injective-hom-prop-map-Precategory =
    is-prop-is-injective-hom-map-Precategory
```

## Properties

### A map of precategories is faithful if and only if it is injective on hom-sets

```agda
module _
  {l1 l2 l3 l4 : Level}
  (C : Precategory l1 l2)
  (D : Precategory l3 l4)
  (F : map-Precategory C D)
  where

  is-injective-hom-is-faithful-map-Precategory :
    is-faithful-map-Precategory C D F →
    is-injective-hom-map-Precategory C D F
  is-injective-hom-is-faithful-map-Precategory is-faithful-F x y =
    is-injective-is-emb (is-faithful-F x y)

  is-faithful-is-injective-hom-map-Precategory :
    is-injective-hom-map-Precategory C D F →
    is-faithful-map-Precategory C D F
  is-faithful-is-injective-hom-map-Precategory is-injective-hom-F x y =
    is-emb-is-injective
      ( is-set-hom-Precategory D
        ( obj-map-Precategory C D F x)
        ( obj-map-Precategory C D F y))
      ( is-injective-hom-F x y)

  is-equiv-is-injective-hom-is-faithful-map-Precategory :
    is-equiv is-injective-hom-is-faithful-map-Precategory
  is-equiv-is-injective-hom-is-faithful-map-Precategory =
    is-equiv-is-prop
      ( is-prop-is-faithful-map-Precategory C D F)
      ( is-prop-is-injective-hom-map-Precategory C D F)
      ( is-faithful-is-injective-hom-map-Precategory)

  is-equiv-is-faithful-is-injective-hom-map-Precategory :
    is-equiv is-faithful-is-injective-hom-map-Precategory
  is-equiv-is-faithful-is-injective-hom-map-Precategory =
    is-equiv-is-prop
      ( is-prop-is-injective-hom-map-Precategory C D F)
      ( is-prop-is-faithful-map-Precategory C D F)
      ( is-injective-hom-is-faithful-map-Precategory)

  equiv-is-injective-hom-is-faithful-map-Precategory :
    is-faithful-map-Precategory C D F ≃
    is-injective-hom-map-Precategory C D F
  pr1 equiv-is-injective-hom-is-faithful-map-Precategory =
    is-injective-hom-is-faithful-map-Precategory
  pr2 equiv-is-injective-hom-is-faithful-map-Precategory =
    is-equiv-is-injective-hom-is-faithful-map-Precategory

  equiv-is-faithful-is-injective-hom-map-Precategory :
    is-injective-hom-map-Precategory C D F ≃
    is-faithful-map-Precategory C D F
  pr1 equiv-is-faithful-is-injective-hom-map-Precategory =
    is-faithful-is-injective-hom-map-Precategory
  pr2 equiv-is-faithful-is-injective-hom-map-Precategory =
    is-equiv-is-faithful-is-injective-hom-map-Precategory
```

## See also

- [Faithful functors between precategories](category-theory.faithful-functors-precategories.md)
