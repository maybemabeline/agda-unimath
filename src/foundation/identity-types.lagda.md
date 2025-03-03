# Identity types

```agda
module foundation.identity-types where

open import foundation-core.identity-types public
```

<details><summary>Imports</summary>

```agda
open import foundation.action-on-identifications-functions
open import foundation.binary-equivalences
open import foundation.dependent-pair-types
open import foundation.equivalence-extensionality
open import foundation.function-extensionality
open import foundation.universe-levels

open import foundation-core.equivalences
open import foundation-core.fibers-of-maps
open import foundation-core.function-types
open import foundation-core.homotopies
```

</details>

## Idea

The equality relation on a type is a reflexive relation, with the universal
property that it maps uniquely into any other reflexive relation. In type
theory, we introduce the identity type as an inductive family of types, where
the induction principle can be understood as expressing that the identity type
is the least reflexive relation.

## Table of files directly related to identity types

The following table lists files that are about identity types and operations on
identifications in arbitrary types.

{{#include tables/identity-types.md}}

## Properties

### The Mac Lane pentagon for identity types

```agda
Mac-Lane-pentagon :
  {l : Level} {A : UU l} {a b c d e : A}
  (p : a ＝ b) (q : b ＝ c) (r : c ＝ d) (s : d ＝ e) →
  let α₁ = (ap (λ t → t ∙ s) (assoc p q r))
      α₂ = (assoc p (q ∙ r) s)
      α₃ = (ap (λ t → p ∙ t) (assoc q r s))
      α₄ = (assoc (p ∙ q) r s)
      α₅ = (assoc p q (r ∙ s))
  in
  ((α₁ ∙ α₂) ∙ α₃) ＝ (α₄ ∙ α₅)
Mac-Lane-pentagon refl refl refl refl = refl
```

### The groupoidal operations on identity types are equivalences

```agda
module _
  {l : Level} {A : UU l}
  where

  abstract
    is-equiv-inv : (x y : A) → is-equiv (λ (p : x ＝ y) → inv p)
    is-equiv-inv x y = is-equiv-is-invertible inv inv-inv inv-inv

  equiv-inv : (x y : A) → (x ＝ y) ≃ (y ＝ x)
  pr1 (equiv-inv x y) = inv
  pr2 (equiv-inv x y) = is-equiv-inv x y

  inv-concat : {x y : A} (p : x ＝ y) (z : A) → x ＝ z → y ＝ z
  inv-concat p = concat (inv p)

  is-retraction-inv-concat :
    {x y : A} (p : x ＝ y) (z : A) → (inv-concat p z ∘ concat p z) ~ id
  is-retraction-inv-concat refl z q = refl

  is-section-inv-concat :
    {x y : A} (p : x ＝ y) (z : A) → (concat p z ∘ inv-concat p z) ~ id
  is-section-inv-concat refl z refl = refl

  abstract
    is-equiv-concat :
      {x y : A} (p : x ＝ y) (z : A) → is-equiv (concat p z)
    is-equiv-concat p z =
      is-equiv-is-invertible
        ( inv-concat p z)
        ( is-section-inv-concat p z)
        ( is-retraction-inv-concat p z)

  equiv-concat :
    {x y : A} (p : x ＝ y) (z : A) → (y ＝ z) ≃ (x ＝ z)
  pr1 (equiv-concat p z) = concat p z
  pr2 (equiv-concat p z) = is-equiv-concat p z

  map-equiv-concat-equiv :
    {x x' : A} → ((y : A) → (x ＝ y) ≃ (x' ＝ y)) → (x' ＝ x)
  map-equiv-concat-equiv {x} e = map-equiv (e x) refl

  is-section-equiv-concat :
    {x x' : A} → map-equiv-concat-equiv {x} {x'} ∘ equiv-concat ~ id
  is-section-equiv-concat refl = refl

  abstract
    is-retraction-equiv-concat :
      {x x' : A} → equiv-concat ∘ map-equiv-concat-equiv {x} {x'} ~ id
    is-retraction-equiv-concat e =
      eq-htpy (λ y → eq-htpy-equiv (λ where refl → right-unit))

  abstract
    is-equiv-map-equiv-concat-equiv :
      {x x' : A} → is-equiv (map-equiv-concat-equiv {x} {x'})
    is-equiv-map-equiv-concat-equiv =
      is-equiv-is-invertible
        ( equiv-concat)
        ( is-section-equiv-concat)
        ( is-retraction-equiv-concat)

  equiv-concat-equiv :
    {x x' : A} → ((y : A) → (x ＝ y) ≃ (x' ＝ y)) ≃ (x' ＝ x)
  pr1 equiv-concat-equiv = map-equiv-concat-equiv
  pr2 equiv-concat-equiv = is-equiv-map-equiv-concat-equiv

  inv-concat' : (x : A) {y z : A} → y ＝ z → x ＝ z → x ＝ y
  inv-concat' x q = concat' x (inv q)

  is-retraction-inv-concat' :
    (x : A) {y z : A} (q : y ＝ z) → (inv-concat' x q ∘ concat' x q) ~ id
  is-retraction-inv-concat' x refl refl = refl

  is-section-inv-concat' :
    (x : A) {y z : A} (q : y ＝ z) → (concat' x q ∘ inv-concat' x q) ~ id
  is-section-inv-concat' x refl refl = refl

  abstract
    is-equiv-concat' :
      (x : A) {y z : A} (q : y ＝ z) → is-equiv (concat' x q)
    is-equiv-concat' x q =
      is-equiv-is-invertible
        ( inv-concat' x q)
        ( is-section-inv-concat' x q)
        ( is-retraction-inv-concat' x q)

  equiv-concat' :
    (x : A) {y z : A} (q : y ＝ z) → (x ＝ y) ≃ (x ＝ z)
  pr1 (equiv-concat' x q) = concat' x q
  pr2 (equiv-concat' x q) = is-equiv-concat' x q

is-binary-equiv-concat :
  {l : Level} {A : UU l} {x y z : A} →
  is-binary-equiv (λ (p : x ＝ y) (q : y ＝ z) → p ∙ q)
pr1 (is-binary-equiv-concat {x = x}) q = is-equiv-concat' x q
pr2 (is-binary-equiv-concat {z = z}) p = is-equiv-concat p z

equiv-binary-concat :
  {l : Level} {A : UU l} {x y z w : A} → (p : x ＝ y) (q : z ＝ w) →
  (y ＝ z) ≃ (x ＝ w)
equiv-binary-concat {x = x} {z = z} p q =
  (equiv-concat' x q) ∘e (equiv-concat p z)

convert-eq-values :
  {l1 l2 : Level} {A : UU l1} {B : UU l2} {f g : A → B} (H : f ~ g)
  (x y : A) → (f x ＝ f y) ≃ (g x ＝ g y)
convert-eq-values {f = f} {g} H x y =
  ( equiv-concat' (g x) (H y)) ∘e (equiv-concat (inv (H x)) (f y))

module _
  {l1 : Level} {A : UU l1}
  where

  is-section-is-injective-concat :
    {x y z : A} (p : x ＝ y) {q r : y ＝ z} (s : (p ∙ q) ＝ (p ∙ r)) →
    ap (concat p z) (is-injective-concat p s) ＝ s
  is-section-is-injective-concat refl refl = refl

  cases-is-section-is-injective-concat' :
    {x y : A} {p q : x ＝ y} (s : p ＝ q) →
    ( ap
      ( concat' x refl)
      ( is-injective-concat' refl (right-unit ∙ (s ∙ inv right-unit)))) ＝
    ( right-unit ∙ (s ∙ inv right-unit))
  cases-is-section-is-injective-concat' {p = refl} refl = refl

  is-section-is-injective-concat' :
    {x y z : A} (r : y ＝ z) {p q : x ＝ y} (s : (p ∙ r) ＝ (q ∙ r)) →
    ap (concat' x r) (is-injective-concat' r s) ＝ s
  is-section-is-injective-concat' refl s =
    ( ap (λ u → ap (concat' _ refl) (is-injective-concat' refl u)) (inv α)) ∙
    ( ( cases-is-section-is-injective-concat'
        ( inv right-unit ∙ (s ∙ right-unit))) ∙
      ( α))
    where
    α :
      ( ( right-unit) ∙
        ( ( inv right-unit ∙ (s ∙ right-unit)) ∙
          ( inv right-unit))) ＝
      ( s)
    α =
      ( ap
        ( concat right-unit _)
        ( ( assoc (inv right-unit) (s ∙ right-unit) (inv right-unit)) ∙
          ( ( ap
              ( concat (inv right-unit) _)
              ( ( assoc s right-unit (inv right-unit)) ∙
                ( ( ap (concat s _) (right-inv right-unit)) ∙
                  ( right-unit))))))) ∙
      ( ( inv (assoc right-unit (inv right-unit) s)) ∙
        ( ( ap (concat' _ s) (right-inv right-unit))))
```

## Transposing inverses is an equivalence

```agda
module _
  {l : Level} {A : UU l} {x y z : A}
  where

  abstract
    is-equiv-left-transpose-eq-concat :
      (p : x ＝ y) (q : y ＝ z) (r : x ＝ z) →
      is-equiv (left-transpose-eq-concat p q r)
    is-equiv-left-transpose-eq-concat refl q r = is-equiv-id

  equiv-left-transpose-eq-concat :
    (p : x ＝ y) (q : y ＝ z) (r : x ＝ z) →
    ((p ∙ q) ＝ r) ≃ (q ＝ ((inv p) ∙ r))
  pr1 (equiv-left-transpose-eq-concat p q r) = left-transpose-eq-concat p q r
  pr2 (equiv-left-transpose-eq-concat p q r) =
    is-equiv-left-transpose-eq-concat p q r

  abstract
    is-equiv-right-transpose-eq-concat :
      (p : x ＝ y) (q : y ＝ z) (r : x ＝ z) →
      is-equiv (right-transpose-eq-concat p q r)
    is-equiv-right-transpose-eq-concat p refl r =
      is-equiv-comp
        ( concat' p (inv right-unit))
        ( concat (inv right-unit) r)
        ( is-equiv-concat (inv right-unit) r)
        ( is-equiv-concat' p (inv right-unit))

  equiv-right-transpose-eq-concat :
    (p : x ＝ y) (q : y ＝ z) (r : x ＝ z) →
    ((p ∙ q) ＝ r) ≃ (p ＝ (r ∙ (inv q)))
  pr1 (equiv-right-transpose-eq-concat p q r) = right-transpose-eq-concat p q r
  pr2 (equiv-right-transpose-eq-concat p q r) =
    is-equiv-right-transpose-eq-concat p q r
```

### Computation of fibers of families of maps out of the identity type

We show that `fiber (f x) y ≃ ((* , f * refl) ＝ (x , y))` for every `x : A` and
`y : B x`.

```agda
module _
  {l1 l2 : Level} {A : UU l1} {a : A} {B : A → UU l2}
  (f : (x : A) → (a ＝ x) → B x) (x : A) (y : B x)
  where

  map-compute-fiber-map-out-of-identity-type :
    fiber (f x) y → ((a , f a refl) ＝ (x , y))
  map-compute-fiber-map-out-of-identity-type (refl , refl) = refl

  map-inv-compute-fiber-map-out-of-identity-type :
    ((a , f a refl) ＝ (x , y)) → fiber (f x) y
  map-inv-compute-fiber-map-out-of-identity-type refl =
    refl , refl

  is-section-map-inv-compute-fiber-map-out-of-identity-type :
    map-compute-fiber-map-out-of-identity-type ∘
    map-inv-compute-fiber-map-out-of-identity-type ~ id
  is-section-map-inv-compute-fiber-map-out-of-identity-type refl = refl

  is-retraction-map-inv-compute-fiber-map-out-of-identity-type :
    map-inv-compute-fiber-map-out-of-identity-type ∘
    map-compute-fiber-map-out-of-identity-type ~ id
  is-retraction-map-inv-compute-fiber-map-out-of-identity-type (refl , refl) =
    refl

  is-equiv-map-compute-fiber-map-out-of-identity-type :
    is-equiv map-compute-fiber-map-out-of-identity-type
  is-equiv-map-compute-fiber-map-out-of-identity-type =
    is-equiv-is-invertible
      map-inv-compute-fiber-map-out-of-identity-type
      is-section-map-inv-compute-fiber-map-out-of-identity-type
      is-retraction-map-inv-compute-fiber-map-out-of-identity-type

  compute-fiber-map-out-of-identity-type :
    fiber (f x) y ≃ ((a , f a refl) ＝ (x , y))
  pr1 compute-fiber-map-out-of-identity-type =
    map-compute-fiber-map-out-of-identity-type
  pr2 compute-fiber-map-out-of-identity-type =
    is-equiv-map-compute-fiber-map-out-of-identity-type
```
