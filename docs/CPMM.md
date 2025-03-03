# Constant Product Market Maker (CPMM) Documentation

The Constant Product Market Maker (CPMM) module provides functionality for traditional AMM pools that use the constant product formula (x * y = k).

## Cpmm Interface

The main interface for interacting with CPMM functionality.

```typescript
interface Cpmm {
  // Create a new CPMM pool
  createPool(props: CreatePoolParams): Promise<MakeTxData>;
  
  // Add liquidity to an existing CPMM pool
  addLiquidity(props: AddLiquidityParams): Promise<MakeTxData>;
  
  // Remove liquidity from a CPMM pool
  removeLiquidity(props: RemoveLiquidityParams): Promise<MakeTxData>;
  
  // Swap tokens in a CPMM pool
  swap(props: SwapParams): Promise<MakeTxData>;
}
```

## CPMM Parameter Interfaces

### CreatePoolParams

Parameters for creating a new CPMM pool.

```typescript
interface CreatePoolParams {
  programId: PublicKey;            // CPMM program ID
  baseToken: TokenInfo;            // Base token information
  quoteToken: TokenInfo;           // Quote token information
  baseAmount: BN;                  // Initial base token amount
  quoteAmount: BN;                 // Initial quote token amount
  startTime: number;               // Start time (Unix timestamp)
  ownerInfo: { 
    feePayer: PublicKey;           // Fee payer public key
  };
  feeRate: number;                 // Fee rate for the pool
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### AddLiquidityParams

Parameters for adding liquidity to a CPMM pool.

```typescript
interface AddLiquidityParams {
  poolInfo: PoolInfo;              // Pool information
  baseMint: PublicKey;             // Base token mint
  baseAmount: BN;                  // Base token amount to add
  quoteMint: PublicKey;            // Quote token mint
  quoteAmount: BN;                 // Quote token amount to add
  fixedSide: "base" | "quote";     // Which token amount is fixed
  ownerInfo: {                     // Owner information
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### RemoveLiquidityParams

Parameters for removing liquidity from a CPMM pool.

```typescript
interface RemoveLiquidityParams {
  poolInfo: PoolInfo;              // Pool information
  lpAmount: BN;                    // LP token amount to burn
  baseMinAmount: BN;               // Minimum base token to receive
  quoteMinAmount: BN;              // Minimum quote token to receive
  ownerInfo: {                     // Owner information
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### SwapParams

Parameters for swapping tokens in a CPMM pool.

```typescript
interface SwapParams {
  poolInfo: PoolInfo;              // Pool information
  inputMint: PublicKey;            // Input token mint
  amountIn: BN;                    // Input amount
  amountOutMin: BN;                // Minimum output amount
  ownerInfo: {                     // Owner information
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

## CPMM Data Interfaces

### PoolInfo

Information about a CPMM pool.

```typescript
interface PoolInfo {
  id: string;                  // Pool ID
  baseMint: string;            // Base token mint address
  quoteMint: string;           // Quote token mint address
  lpMint: string;              // LP token mint address
  baseDecimals: number;        // Base token decimal places
  quoteDecimals: number;       // Quote token decimal places
  lpDecimals: number;          // LP token decimal places
  version: number;             // Pool version
  programId: string;           // Program ID
  authority: string;           // Pool authority
  openTime: number;            // Pool open time
  baseVault: string;           // Base token vault
  quoteVault: string;          // Quote token vault
  status: PoolStatus;          // Pool status
  feeRate: number;             // Fee rate
}
```

### PoolStatus

Enum for pool status.

```typescript
enum PoolStatus {
  UNINITIALIZED = 0,          // Uninitialized
  INITIALIZED = 1,            // Initialized but not yet open
  OPEN = 2,                   // Open and active
  DISABLED = 3                // Disabled
}
```

### TokenInfo

Information about a token.

```typescript
interface TokenInfo {
  symbol: string;             // Token symbol
  name: string;               // Token name
  mint: string;               // Mint address
  decimals: number;           // Decimal places
  logoURI?: string;           // Logo URI (optional)
  tags?: string[];            // Token tags (optional)
  extensions?: {              // Extensions (optional)
    address?: string;         // Alternative address
    assetContract?: string;   // Asset contract
    bridgeContract?: string;  // Bridge contract
    coingeckoId?: string;     // Coingecko ID
    twitter?: string;         // Twitter handle
    website?: string;         // Website
  };
}
```

## Notes on Using CPMM

1. **Constant Product Formula**: CPMM pools use the formula `x * y = k` where:
   - `x` is the amount of base token
   - `y` is the amount of quote token
   - `k` is a constant value maintained after swaps (minus fees)

2. **Slippage Protection**: Always set reasonable minimum amounts when removing liquidity or swapping to protect against slippage.

3. **LP Tokens**: When adding liquidity, you receive LP tokens representing your share of the pool.

4. **Fixed Side**: When adding liquidity, you specify which token amount is fixed, and the other amount will be calculated based on the current pool ratio.

5. **SOL Handling**: Set `useSOLBalance: true` to automatically handle native SOL wrapping/unwrapping.

6. **Pool Creation**: When creating a pool, consider setting an appropriate fee rate based on expected volatility. Higher volatility pairs typically have higher fees.

7. **Start Time**: The `startTime` parameter in pool creation determines when the pool becomes active. Use `0` for immediate activation.
