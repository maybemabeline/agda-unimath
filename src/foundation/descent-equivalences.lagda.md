# Descent for equivalences

```agda
module foundation.descent-equivalences where
```

<details><summary>Imports</summary>

```agda
open import foundation.cones-over-cospans
open import foundation.equivalences
open import foundation.functoriality-fibers-of-maps
open import foundation.universe-levels

open import foundation-core.function-types
open import foundation-core.functoriality-dependent-function-types
open import foundation-core.pullbacks
```

</details>

## Idea

The descent property of equivalences is a somewhat degenerate form of a descent
property. It asserts that in a commuting diagram of the form

```text
     p        q
 A -----> B -----> C
 |        |        |
f|       g|        |h
 V        V        V
 X -----> Y -----> Z,
     i        j
```

if the maps `i` and `p` are equivalences, then the right square is a pullback if
and only if the outer rectangle is a pullback.

## Theorem

```agda
module _
  {l1 l2 l3 l4 l5 l6 : Level}
  {A : UU l1} {B : UU l2} {C : UU l3} {X : UU l4} {Y : UU l5} {Z : UU l6}
  where

  descent-is-equiv :
    (i : X → Y) (j : Y → Z) (h : C → Z)
    (c : cone j h B) (d : cone i (vertical-map-cone j h c) A) →
    is-equiv i → is-equiv (horizontal-map-cone i (vertical-map-cone j h c) d) →
    is-pullback (j ∘ i) h (pasting-horizontal-cone i j h c d) →
    is-pullback j h c
  descent-is-equiv i j h c d
    is-equiv-i is-equiv-k is-pb-rectangle =
    is-pullback-is-fiberwise-equiv-map-fiber-cone j h c
      ( map-inv-is-equiv-precomp-Π-is-equiv
        ( is-equiv-i)
        ( λ y → is-equiv (map-fiber-cone j h c y))
        ( λ x → is-equiv-left-factor-htpy
          ( map-fiber-cone (j ∘ i) h
            ( pasting-horizontal-cone i j h c d) x)
          ( map-fiber-cone j h c (i x))
          ( map-fiber-cone i (vertical-map-cone j h c) d x)
          ( map-fiber-pasting-horizontal-cone i j h c d x)
          ( is-fiberwise-equiv-map-fiber-cone-is-pullback (j ∘ i) h
            ( pasting-horizontal-cone i j h c d)
            ( is-pb-rectangle)
            ( x))
          ( is-fiberwise-equiv-map-fiber-cone-is-pullback i
            ( vertical-map-cone j h c)
            ( d)
            ( is-pullback-is-equiv' i
              ( vertical-map-cone j h c)
              ( d)
              ( is-equiv-i)
              ( is-equiv-k))
            ( x))))

  descent-equiv :
    (i : X ≃ Y) (j : Y → Z) (h : C → Z)
    (c : cone j h B) (d : cone (map-equiv i) (vertical-map-cone j h c) A) →
    is-equiv (horizontal-map-cone (map-equiv i) (vertical-map-cone j h c) d) →
    is-pullback
      ( j ∘ map-equiv i)
      ( h)
      ( pasting-horizontal-cone (map-equiv i) j h c d) →
    is-pullback j h c
  descent-equiv i j h c d =
    descent-is-equiv (map-equiv i) j h c d (is-equiv-map-equiv i)
```
