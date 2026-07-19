---
name: erc20-ops
description: >
  Read and transfer ERC20 tokens with cast. Use for balanceOf, decimals, symbol, transfer, and token metadata before DeFi actions.
---


# ERC20 ops (cast)

## Goal
Inspect ERC20 metadata/balances and transfer tokens via Foundry cast.

## Prerequisites
- `cast` available (`foundry-agent`)
- `ETH_RPC_URL` or `--rpc-url`
- Token address from an address table (never invent)

## Safety
- Reads are free; `transfer` requires user confirmation (see `foundry-agent-safety`).
- Never print `$PRIVATE_KEY`.

## Inspect
```bash
export TOKEN=0x...   # from address book
export USER=0x...

cast call $TOKEN "name()(string)" --rpc-url $ETH_RPC_URL
cast call $TOKEN "symbol()(string)" --rpc-url $ETH_RPC_URL
cast call $TOKEN "decimals()(uint8)" --rpc-url $ETH_RPC_URL
cast call $TOKEN "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
cast call $TOKEN "totalSupply()(uint256)" --rpc-url $ETH_RPC_URL
```

## Dry-run
No state change for reads. For transfers, re-check balance and recipient.

## Execute (transfer)
```bash
cast send $TOKEN "transfer(address,uint256)" $TO $AMOUNT \
  --rpc-url $ETH_RPC_URL --private-key $PRIVATE_KEY
```

## Verify
```bash
cast call $TOKEN "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
cast call $TOKEN "balanceOf(address)(uint256)" $TO --rpc-url $ETH_RPC_URL
cast receipt $TX --rpc-url $ETH_RPC_URL
```

## Common failures
- Wrong decimals -> amount off by 1eN
- Insufficient balance
- Fee-on-transfer tokens (balance delta != amount)
