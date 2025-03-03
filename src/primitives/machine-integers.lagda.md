# Machine integers

```agda
module primitives.machine-integers where
```

<details><summary>Imports</summary>

```agda
open import elementary-number-theory.natural-numbers

open import foundation.identity-types
open import foundation.universe-levels
```

</details>

## Idea

The `Word64` type represents 64-bit machine words. Agda provides primitive
functions to manipulate them.

## Definitions

```agda
postulate Word64 : UU lzero
{-# BUILTIN WORD64 Word64 #-}

primitive
  primWord64ToNat : Word64 → ℕ
  primWord64FromNat : ℕ → Word64
  primWord64ToNatInjective :
    ∀ a b → primWord64ToNat a ＝ primWord64ToNat b → a ＝ b
```
