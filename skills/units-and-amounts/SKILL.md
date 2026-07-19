---
name: units-and-amounts
description: >
  Convert human token amounts to raw uint256 units with cast. Use before approve/transfer/swap amounts.
---


# Units and amounts (cast)

## Safety
Prefer read-only first; require user confirmation for any write.

## Verify
Re-check state after any write; confirm receipts when broadcasting.


## Goal
Convert between human-readable amounts and on-chain raw units.

## Recipes
```bash
cast to-wei 1.5 ether
cast from-wei 1500000000000000000

python3 - <<'PY'
decimals=6
human=12.5
print(int(human * 10**decimals))
PY
```

## Agent rules
1. Always read `decimals()` for the token.
2. Never pass human floats into cast send args without conversion.
3. Slippage mins use the same raw unit space as amounts out.
