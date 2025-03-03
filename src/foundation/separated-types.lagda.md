# Separated types

```agda
module foundation.separated-types where
```

<details><summary>Imports</summary>

```agda
open import foundation.dependent-pair-types
open import foundation.equivalences
open import foundation.identity-types
open import foundation.propositions
open import foundation.subuniverses
open import foundation.universe-levels
```

</details>

## Idea

Consider a [subuniverse](foundation.subuniverses.md) `P`. A type `A` is said to
be **`P`-separated** if its [identity types](foundation-core.identity-types.md)
are in `P`. Similarly, a type `A` is said to be **essentially `P`-separated** if
its [identity types](foundation-core.identity-types.md) are
[equivalent](foundation-core.equivalences.md) to types in `P`.

## Definitions

### The predicate of being separated

```agda
module _
  {l1 l2 : Level} (P : subuniverse l1 l2)
  where

  is-separated-Prop : (X : UU l1) → Prop (l1 ⊔ l2)
  is-separated-Prop X = Π-Prop X (λ x → Π-Prop X (λ y → P (x ＝ y)))

  is-separated : (X : UU l1) → UU (l1 ⊔ l2)
  is-separated X = type-Prop (is-separated-Prop X)

  is-prop-is-separated : (X : UU l1) → is-prop (is-separated X)
  is-prop-is-separated X = is-prop-type-Prop (is-separated-Prop X)
```

### The predicate of being essentially separated

```agda
module _
  {l1 l2 : Level} (P : subuniverse l1 l2)
  where

  is-essentially-separated : {l3 : Level} (X : UU l3) → UU (lsuc l1 ⊔ l2 ⊔ l3)
  is-essentially-separated X =
    (x y : X) → is-essentially-in-subuniverse P (x ＝ y)
```
