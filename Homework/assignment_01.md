# Logical Verification Assignment 1

> Zifeng Ma 2840668

### Problem 1: Order of variables in typing context

* Let `Δ = Γ, x : τ₁, y : τ₂`
* Proof by Induction on the Structure of the Derivation `D` of `Δ ⊢ e : τ`*

1. `e = num[n]` (Rule T-Num): `Δ ⊢ num[n] : num`
    *   The T-Num rule states that `C ⊢ num[n] : num` for any typing context `C`. The rule does not depend on the specific contents or internal order of `Δ`.
    *   Since `Δ'` is also a valid typing context, `Δ' ⊢ num[n] : num` is also derivable using the same rule.
    *   Thus, the lemma holds for `e = num[n]`.

2.  `e = str[s]` (Rule T-Str):
    *   Analogous to `e = num[n]`. The T-Str rule also does not depend on the context's internal structure.
    *   If `Δ ⊢ str[s] : str`, then `Δ' ⊢ str[s] : str`.
    *   Thus, the lemma holds for `e = str[s]`.

3.  `e = z` (Rule T-Var):
    *   The derivation `D` is: `Δ ⊢ z : τ_z`
    *   3a: `z = x`
        *   `Δ ⊢ x : τ₁` holds because `x : τ₁` is present in `Δ = Γ, x : τ₁, y : τ₂`, and it is the rightmost declaration of `x`.
        *   In `Δ' = Γ, y : τ₂, x : τ₁`, `x : τ₁` is still the rightmost declaration of `x`. Therefore, `Δ' ⊢ x : τ₁` also holds.
    *   3b: `z = y`
        *   `Δ ⊢ y : τ₂` holds because `y : τ₂` is present in `Δ = Γ, x : τ₁, y : τ₂`, and it is the rightmost declaration of `y`.
        *   In `Δ' = Γ, y : τ₂, x : τ₁`, `y : τ₂` is still the rightmost declaration of `y`. Therefore, `Δ' ⊢ y : τ₂` also holds.
    *   3c: `z ∈ Γ` and `z` is neither `x` nor `y`
        *   `Δ ⊢ z : τ_z` holds because `z : τ_z ∈ Γ`. Since `x` and `y` are distinct from `z`, the binding `z : τ_z` from `Γ` remains the rightmost binding for `z` in `Δ`.
        *   Similarly, in `Δ' = Γ, y : τ₂, x : τ₁`, the binding `z : τ_z` from `Γ` remains the rightmost binding for `z`. Therefore, `Δ' ⊢ z : τ_z` also holds.
    *   Thus, the lemma holds for `e = z`.

4.  `e = plus(e₁, e₂)` (Rule T-Plus):
    *  `Δ ⊢ e₁ : num,  Δ ⊢ e₂ : num, Δ ⊢ plus(e₁, e₂) : num`
    *   By IH, since `Δ ⊢ e₁ : num` is a sub-derivation, it follows that `Δ' ⊢ e₁ : num`.
    *   By IH, since `Δ ⊢ e₂ : num` is a sub-derivation, it follows that `Δ' ⊢ e₂ : num`.
    *   Applying the T-Plus rule to these derived judgments:
        `Δ' ⊢ e₁ : num, Δ' ⊢ e₂ : num, Δ' ⊢ plus(e₁, e₂) : num`
    *   Thus, the lemma holds for `e = plus(e₁, e₂)`.

5.  `e = times(e₁, e₂)` (Rule T-Times) and `e = cat(e₁, e₂)` (Rule T-Cat):
    *   These cases are analogous to `plus(e₁, e₂)` with the corresponding type requirements. For each, apply the IH to the premises `Δ ⊢ e₁ : τ_A` and `Δ ⊢ e₂ : τ_B`, yielding `Δ' ⊢ e₁ : τ_A` and `Δ' ⊢ e₂ : τ_B`, respectively. Then re-apply the T-Times or T-Cat rule to obtain `Δ' ⊢ e : τ`.
    *   Thus, the lemma holds for these cases.

6.  `e = len(e₀)` (Rule T-Len):
    *   The derivation `D` ends with: `Δ ⊢ e₀ : str, Δ ⊢ len(e₀) : num`
    *   By IH, since `Δ ⊢ e₀ : str` is a sub-derivation, it follows that `Δ' ⊢ e₀ : str`.
    *   Applying the T-Len rule to this derived judgment:
        `Δ' ⊢ e₀ : str, Δ' ⊢ len(e₀) : num`
    *   Thus, the lemma holds for `e = len(e₀)`.

7.  `e = let z be e₁ in e₂`
    *   `Δ ⊢ e₁ : τ₁', Δ, z : τ₁' ⊢ e₂ : τ`
    *   `Δ ⊢ let z be e₁ in e₂ : τ`
     *   By IH, since `Δ ⊢ e₁ : τ₁'` is a sub-derivation, it follows that `Δ' ⊢ e₁ : τ₁'`.

    *   Now consider the second premise: `Δ, z : τ₁' ⊢ e₂ : τ`.
        *   `Δ, z : τ₁'` is `(Γ, x : τ₁, y : τ₂), z : τ₁'`.
        *   The corresponding context for `Δ'` would be `Δ', z : τ₁'`, which is `(Γ, y : τ₂, x : τ₁), z : τ₁'`.
        *   The inductive hypothesis applies to contexts where `x` and `y` are adjacent and their order is swapped. This scenario fits the pattern: let `C_base = Γ`. We are comparing `C_base, x : τ₁, y : τ₂, z : τ₁' ⊢ e₂ : τ` with `C_base, y : τ₂, x : τ₁, z : τ₁' ⊢ e₂ : τ`. The key is that `z : τ₁'` is further to the right (more recent) than both `x : τ₁` and `y : τ₂`. Swapping `x` and `y` only affects their relative order, not their relation to `z` or to any bindings in `Γ`.
        *   Therefore, by IH, since `(Γ, x : τ₁, y : τ₂), z : τ₁' ⊢ e₂ : τ` holds, then `(Γ, y : τ₂, x : τ₁), z : τ₁' ⊢ e₂ : τ` also holds. This is equivalent to `Δ', z : τ₁' ⊢ e₂ : τ`.

    *   Applying the T-Let rule: `Δ' ⊢ e₁ : τ₁', Δ', z : τ₁' ⊢ e₂ : τ, Δ' ⊢ let z be e₁ in e₂ : τ`
    *   Thus, the lemma holds for `e = let z be e₁ in e₂`.

### Problem 2: Language Extension with Booleans and Conditionals

1. Typing Rules for New Constructs

These rules define how to determine the type of the new expressions.

*   Type: `Bool`
    *   `Bool` is a new base type that extends the set of possible types `τ`. It doesn't have a derivation rule itself, but is used in the types of expressions.

*   `true` (Rule T-True)
    *  The literal `true` directly has the type `Bool`. `Γ ⊢ true : Bool`

*   `false` (Rule T-False)
    *   Same as above. `Γ ⊢ false : Bool`

*   Expression: `if e then e₁ else e₂` (Rule T-If)
    *   For an `if` expression to be well-typed, its condition `e` must be of type `Bool`. Both the `then` branch `e₁` and the `else` branch `e₂` must have the same type `τ`. The entire `if` expression then evaluates to that common type `τ`.

2. Small-Step Semantics for New Constructs

*   `true` and `false` are already values; they do not reduce further.

*   `if e then e₁ else e₂`

    *   `if true then e₁ else e₂ --> e₁`

    *   `if false then e₁ else e₂ --> e₂`

    *   If the condition `e` has not yet evaluated to a `true` or `false` value, it must be evaluated first. This is a congruence rule, allowing evaluation to proceed within the `if` construct.
        ```
        e --> e'
        ---------------------------------------------- (E-If)
        if e then e₁ else e₂ --> if e' then e₁ else e₂
        ```

### Problem 3: Call-by-Value `let` and Type Safety

1. Adapted Operational Semantics Rules for Call-by-Value `let`

*   Rule E-Let1 (Evaluate `e₁`):
```
    e₁ --> e₁'
    ---------------------------------------------- (E-Let1)
    let x be e₁ in e₂ --> let x be e₁' in e₂
```

-  Rule E-LetVal (Substitute Value):
```
 ------------------------------------------- (E-LetVal)
    let x be v in e₂ --> e₂[v/x]
```

2. Proving Type Safety for `let x be e₁ in e₂`

Proof for `e = let x be e₁ in e₂`:
*   Assumption: `Γ ⊢ let x be e₁ in e₂ : τ` and `let x be e₁ in e₂ --> e'`.
*   From T-Let rule:
    1.  `Γ ⊢ e₁ : τ₁'`
    2.  `Γ, x : τ₁' ⊢ e₂ : τ`

*   Case A: `let x be e₁ in e₂ --> let x be e₁' in e₂` by E-Let1 (where `e₁ --> e₁'`).
    *   By IH on `e₁`: `Γ ⊢ e₁' : τ₁'`.
    *   Using `Γ ⊢ e₁' : τ₁'` and `Γ, x : τ₁' ⊢ e₂ : τ` (from 2), apply T-Let: `Γ ⊢ let x be e₁' in e₂ : τ`.
    *   Thus, `Γ ⊢ e' : τ`.

*   Case B: `let x be v in e₂ --> e₂[v/x]` by E-LetVal (where `e₁` is a value `v`).
    *   Since `e₁ = v`, from (1): `Γ ⊢ v : τ₁'`.
    *   Using `Γ ⊢ v : τ₁'` and `Γ, x : τ₁' ⊢ e₂ : τ` (from 2), by Substitution Lemma: `Γ ⊢ e₂[v/x] : τ`.
    *   Thus, `Γ ⊢ e' : τ`.

*   Conclusion: Preservation holds for `let x be e₁ in e₂`.

Proof for `e = let x be e₁ in e₂`:
*   Assumption: `Γ ⊢ let x be e₁ in e₂ : τ`.
*   From T-Let rule:
    1.  `Γ ⊢ e₁ : τ₁'`
    2.  `Γ, x : τ₁' ⊢ e₂ : τ`
*   `let x be e₁ in e₂` is not a value.

*   Need to show `let x be e₁ in e₂` can take a step (`e --> e'`):
    *   From (1), we have `Γ ⊢ e₁ : τ₁'`.
    *   By IH on `e₁` (Progress for sub-expression):
        *   Subcase A: `e₁` is a value `v`.
            *   Then by E-LetVal: `let x be v in e₂ --> e₂[v/x]`.
        *   Subcase B: `e₁` can take a step (`e₁ --> e₁'`).
            *   Then by E-Let1: `let x be e₁ in e₂ --> let x be e₁' in e₂`.

*   Conclusion: In both sub-cases for `e₁`, `let x be e₁ in e₂` can take a step. Thus, Progress holds for `let x be e₁ in e₂`.