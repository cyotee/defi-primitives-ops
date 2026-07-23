---
name: approval-ops
description: >
  ERC20 approve / allowance patterns with cast: inspect allowance, approve,
  reset-to-zero for USDT-like tokens, verify. Use before router or vault spends.
---

# ERC20 approval ops (cast)

## Goal

Set or clear ERC20 allowances for a spender (router, vault, Permit2) safely.

## Prerequisites

- Token + spender addresses from address tables
- `foundry-agent-safety` before writes
- `USER` = agent wallet

```bash
export TOKEN=0x...
export SPENDER=0x...
export USER=$(cast wallet address --private-key $PRIVATE_KEY)
```

## Inspect

```bash
cast call $TOKEN "allowance(address,address)(uint256)" $USER $SPENDER --rpc-url $ETH_RPC_URL
cast call $TOKEN "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
cast call $TOKEN "decimals()(uint8)" --rpc-url $ETH_RPC_URL
```

## Dry-run

Reads only. Plan amount: prefer exact amount over max `type(uint256).max` when possible.

## Execute — standard approve

```bash
cast send $TOKEN "approve(address,uint256)" $SPENDER $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
```

## Execute — USDT-like reset (non-zero → non-zero may revert)

```bash
# 1) reset
cast send $TOKEN "approve(address,uint256)" $SPENDER 0 \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
# 2) set new amount
cast send $TOKEN "approve(address,uint256)" $SPENDER $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
```

## Verify

```bash
cast call $TOKEN "allowance(address,address)(uint256)" $USER $SPENDER --rpc-url $ETH_RPC_URL
cast receipt $TX --rpc-url $ETH_RPC_URL
```

## Common failures

- USDT-style tokens require zero-first
- Wrong spender (Vault vs Router vs Permit2)
- Amount units ignore decimals

## Related

- `erc20-ops`, `permit2-allowance-cast` (when spender pulls via Permit2)
