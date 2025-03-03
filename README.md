# Raydium SDK V2 Documentation

This repository contains comprehensive documentation for the Raydium SDK V2, a JavaScript/TypeScript SDK for interacting with the Raydium Protocol on Solana.

## Overview

Raydium SDK V2 provides a set of modules for interacting with different aspects of the Raydium protocol:

- **Concentrated Liquidity Market Maker (CLMM)**: For creating and managing concentrated liquidity positions
- **Constant Product Market Maker (CPMM)**: For traditional AMM pool interactions
- **Farm**: For staking LP tokens and earning rewards
- **Liquidity**: For managing liquidity in pools
- **Trading**: For swapping tokens through Raydium's liquidity pools

## Installation

```bash
npm install @raydium-io/raydium-sdk-v2 @solana/web3.js @solana/spl-token
```

## Module Structure

The SDK is organized into several modules, each handling different aspects of the Raydium protocol:

```
Raydium SDK V2
├── Raydium (main class)
├── TokenModule
├── Account
├── Api
├── Clmm
├── Cpmm
├── Farm
├── Liquidity
└── TradeV2
```

## Documentation

- [Core Modules](./docs/Core-Modules.md) - Main Raydium class and core modules
- [CLMM (Concentrated Liquidity)](./docs/CLMM.md) - CLMM interfaces and operations
- [CPMM (Constant Product)](./docs/CPMM.md) - CPMM interfaces and operations
- [Farm](./docs/Farm.md) - Farm interaction interfaces
- [Liquidity & Trading](./docs/Liquidity-Trade.md) - Liquidity and trading operations
- [Account & API](./docs/Account-API.md) - Account and API interaction interfaces
- [Common Types](./docs/Common-Types.md) - Common types and utilities

## Development Environment

### Required Packages

```bash
npm install @solana/spl-token @solana/web3.js @raydium-io/raydium-sdk-v2
```

### Test Validator Setup

For local development, you can use the following command to start a Solana test validator with the necessary programs:

```bash
solana-test-validator -r \
--bpf-program TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb ./token_2022.so \
--bpf-program metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s ./mpl-token-metadata.so \
--bpf-program BGUMAp9Gq7iTEuizy4pqaxsTyUCBK68MDfK752saRPUY ./mpl-bubblegum.so \
--bpf-program CoREENxT6tW1HoK8ypY1SxRMZTcVPm7R94rH4PZNhX7d ./mpl-core.so \
--bpf-program ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL ./associated_token_account.so \
--bpf-program TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA ./spl_token.so \
--bpf-program CAMMCzo5YL8w4VFF8KVHrK22GGUsp5VTaW7grrKgrWqK ./raydium_clmm_program.so \
--bpf-program CPMMoo8L3F4NbTegBCKVNunggL7H1ZpdTHKxQB5qKP1C ./create_cpmm_pool_program.so \
--bpf-program 675kPX9MHTjS2zt1qfr1NYHuzeLXfQM9H24wFSUt1Mp8 ./ammv4.so \
--bpf-program 5quBtoiQqxF9Jv6KYKctB59NT3gtJD2Y65kdnB1Uev3h ./amm_stable.so \
--account DNXgeM9EiiaAbaWvwjHj9fQQLAX5ZsfHyvmYUNRAdNC8 ./create_cpmm_pool_fee_acc.json \
--account D4FPEruKEHrG5TenZ2mpDGEfu1iUvTiqBxvpU8HLBvC2 ./amm_config.json
```

## License

MIT
