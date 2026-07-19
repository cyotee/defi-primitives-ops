---
name: approval-ops
description: >
  ERC20 approve and allowance checks with cast. Use before router/vault spends; includes exact approve and USDT-style reset.
---


# Approval ops (cast)

## Goal
Set and verify ERC20 allowances for a spender (router, vault, Permit2).

## Prerequisites
`TOKEN`, `SPENDER`, `USER`, `AMOUNT` (raw units), RPC + signer for writes.

## Safety
- Prefer **exact** amount approvals for agents.
- Infinite approve only with explicit user OK.
- Confirm chain id before `cast send`.

## Inspect
```bash
cast call $TOKEN "allowance(address,address)(uint256)" $USER $SPENDER --rpc-url $ETH_RPC_URL
```

## Dry-run
Confirm allowance < required amount before approving.

## Execute
```bash
cast send $TOKEN "approve(address,uint256)" $SPENDER $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY

# USDT-style reset then set
cast send $TOKEN "approve(address,uint256)" $SPENDER 0 \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
cast send $TOKEN "approve(address,uint256)" $SPENDER $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
```

## Verify
```bash
cast call $TOKEN "allowance(address,address)(uint256)" $USER $SPENDER --rpc-url $ETH_RPC_URL
```

## Common failures
- Allowance still 0 after approve (wrong token/spender)
- Non-standard tokens require 0-reset
