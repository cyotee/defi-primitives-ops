---
name: weth-ops
description: >
  Wrap and unwrap ETH with WETH using cast deposit/withdraw. Use when protocols require WETH instead of native ETH.
---


# WETH ops (cast)

## Goal
Convert native ETH <-> WETH9.

## Prerequisites
WETH address from references/common-tokens.md for the chain.

## Safety
Confirm chain and WETH address. Confirm value before deposit.

## Inspect
```bash
cast call $WETH "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
cast balance $USER --rpc-url $ETH_RPC_URL -e
```

## Execute
```bash
cast send $WETH "deposit()" --value 0.01ether \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY

AMOUNT=$(cast to-wei 0.01 ether)
cast send $WETH "withdraw(uint256)" $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
```

## Verify
```bash
cast call $WETH "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
cast balance $USER --rpc-url $ETH_RPC_URL -e
```

## Common failures
- Wrong WETH address on L2
- Insufficient ETH for gas + value
