# Concentrated Liquidity Market Maker (CLMM) Documentation

The Concentrated Liquidity Market Maker (CLMM) module provides functionality for creating and managing concentrated liquidity positions, similar to Uniswap V3's model.

## Clmm Interface

The main interface for interacting with CLMM functionality.

```typescript
interface Clmm {
  // Get all related public keys for a CLMM pool
  getClmmPoolKeys(poolId: string): Promise<ClmmKeys>;
  
  // Create a new CLMM pool
  createPool(props: CreatePoolParams): Promise<MakeTxData>;
  
  // Open a new liquidity position from base token
  openPositionFromBase(params: OpenPositionFromBaseParams): Promise<MakeTxData>;
  
  // Open a new liquidity position from liquidity amount
  openPositionFromLiquidity(params: OpenPositionFromLiquidityParams): Promise<MakeTxData>;
  
  // Increase position liquidity from base token
  increasePositionFromBase(props: IncreasePositionFromBaseParams): Promise<MakeTxData>;
  
  // Decrease liquidity from a position
  decreaseLiquidity(props: DecreaseLiquidityParams): Promise<MakeTxData>;
  
  // Close a liquidity position completely
  closePosition(params: ClosePositionParams): Promise<MakeTxData>;
  
  // Swap tokens within a CLMM pool
  swap(params: ClmmSwapParams): Promise<MakeTxData>;
  
  // Lock a position for farming rewards
  lockPosition(props: LockPositionParams): Promise<MakeTxData>;
  
  // Harvest rewards from a locked position
  harvestLockPosition(props: HarvestLockPositionParams): Promise<MakeTxData>;
}
```

## CLMM Data Interfaces

### ClmmPoolInfo

Information about a CLMM pool.

```typescript
interface ClmmPoolInfo {
  id: string;                  // Pool ID
  mintA: {                     // Token A information
    address: string;           // Token A address
    decimals: number;          // Token A decimal places
    programId: string;         // Token A program ID (TOKEN_PROGRAM_ID or TOKEN_2022_PROGRAM_ID)
  };
  mintB: {                     // Token B information
    address: string;           // Token B address
    decimals: number;          // Token B decimal places
    programId: string;         // Token B program ID
  };
  ammConfig: {                 // AMM configuration
    id: string;                // Configuration ID
    index: number;             // Configuration index
    protocolFeeRate: number;   // Protocol fee rate
    tradeFeeRate: number;      // Trading fee rate
    tickSpacing: number;       // Tick spacing
  };
  observationId: string;       // Observation account ID
  currentSqrtPrice: BN;        // Current square root price
  currentPrice: Decimal;       // Current price
  liquidity: BN;               // Current pool liquidity
  observationUpdateDuration: number; // Observation update duration
}
```

### ClmmKeys

Account keys related to a CLMM pool.

```typescript
interface ClmmKeys {
  id: PublicKey;               // Pool ID
  mintA: PublicKey;            // Token A mint
  mintB: PublicKey;            // Token B mint
  vaultA: PublicKey;           // Token A vault
  vaultB: PublicKey;           // Token B vault
  observationId: PublicKey;    // Observation account
}
```

### ClmmPosition

Information about a CLMM position.

```typescript
interface ClmmPosition {
  nftMint: PublicKey;          // NFT mint representing the position
  id?: PublicKey;              // Position ID (optional)
  poolId: PublicKey;           // Pool ID
  tickLower: number;           // Lower tick bound
  tickUpper: number;           // Upper tick bound
  liquidity: BN;               // Liquidity amount
}
```

## CLMM Parameter Interfaces

### CreatePoolParams

Parameters for creating a new CLMM pool.

```typescript
interface CreatePoolParams {
  programId: PublicKey;            // CLMM program ID
  owner: PublicKey;                // Owner of the pool
  mint1: {                         // First token information
    address: string;               // Token mint address
    decimals: number;              // Token decimal places
    programId: string;             // Token program ID
  };
  mint2: {                         // Second token information
    address: string;               // Token mint address
    decimals: number;              // Token decimal places
    programId: string;             // Token program ID
  };
  ammConfig: {                     // AMM configuration
    id: PublicKey;                 // Configuration ID
    index: number;                 // Configuration index
    protocolFeeRate: number;       // Protocol fee rate
    tradeFeeRate: number;          // Trading fee rate
    tickSpacing: number;           // Tick spacing
    fundFeeRate?: number;          // Fund fee rate (optional)
    description?: string;          // Description (optional)
  };
  initialPrice: Decimal;           // Initial pool price
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### OpenPositionFromBaseParams

Parameters for opening a position using a base token amount.

```typescript
interface OpenPositionFromBaseParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  poolKeys?: ClmmKeys;             // Pool keys (optional)
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  tickLower: number;               // Lower tick bound
  tickUpper: number;               // Upper tick bound
  base: "MintA" | "MintB";         // Base token (either MintA or MintB)
  baseAmount: BN;                  // Amount of base token to deposit
  otherAmountMax: BN;              // Maximum amount of other token to use
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### OpenPositionFromLiquidityParams

Parameters for opening a position with a specific liquidity amount.

```typescript
interface OpenPositionFromLiquidityParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  poolKeys?: ClmmKeys;             // Pool keys (optional)
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  tickLower: number;               // Lower tick bound
  tickUpper: number;               // Upper tick bound
  liquidity: BN;                   // Liquidity amount to provide
  amountMaxA: BN;                  // Maximum amount of token A to use
  amountMaxB: BN;                  // Maximum amount of token B to use
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### IncreasePositionFromBaseParams

Parameters for increasing a position's liquidity from base token.

```typescript
interface IncreasePositionFromBaseParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  poolKeys?: ClmmKeys;             // Pool keys (optional)
  ownerPosition: ClmmPosition;     // Owner's position information
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  base: "MintA" | "MintB";         // Base token (either MintA or MintB)
  baseAmount: BN;                  // Amount of base token to add
  otherAmountMax: BN;              // Maximum amount of other token to use
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### DecreaseLiquidityParams

Parameters for decreasing a position's liquidity.

```typescript
interface DecreaseLiquidityParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  ownerPosition: ClmmPosition;     // Owner's position information
  ownerInfo: {                     // Owner information
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
    closePosition?: boolean;       // Close the position after decreasing
  };
  liquidity: BN;                   // Liquidity amount to decrease
  amountMinA: BN;                  // Minimum amount of token A to receive
  amountMinB: BN;                  // Minimum amount of token B to receive
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### ClosePositionParams

Parameters for closing a position completely.

```typescript
interface ClosePositionParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  ownerPosition: ClmmPosition;     // Owner's position information
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL 
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### ClmmSwapParams

Parameters for swapping tokens in a CLMM pool.

```typescript
interface ClmmSwapParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  poolKeys?: ClmmKeys;             // Pool keys (optional)
  inputMint: PublicKey;            // Input token mint
  amountIn: BN;                    // Input amount
  amountOutMin: BN;                // Minimum output amount
  priceLimit?: Decimal;            // Price limit (optional)
  observationId: PublicKey;        // Observation ID
  ownerInfo: {                     // Owner information
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  remainingAccounts: any[];        // Additional accounts required
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### LockPositionParams

Parameters for locking a position for farming.

```typescript
interface LockPositionParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  ownerPosition: ClmmPosition;     // Owner's position information
  farmInfo: FarmInfo;              // Farm information
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

### HarvestLockPositionParams

Parameters for harvesting rewards from a locked position.

```typescript
interface HarvestLockPositionParams {
  poolInfo: ClmmPoolInfo;          // Pool information
  ownerPosition: ClmmPosition;     // Owner's position information
  farmInfo: FarmInfo;              // Farm information
  ownerInfo: {                     // Owner information
    feePayer: PublicKey;           // Fee payer public key
    useSOLBalance?: boolean;       // Use native SOL instead of wrapped SOL
  };
  txVersion: TxVersion;            // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;       // Transaction tip configuration (optional)
}
```

## Notes on Using CLMM

1. **Token Ordering**: When creating a pool, tokens must be sorted by their mint addresses (mintA < mintB).

2. **Tick Calculation**: Ticks represent price points in the pool. Price calculation follows this formula:
   ```
   price = 1.0001^tick
   ```

3. **Position NFTs**: Each liquidity position is represented by an NFT, which holds metadata about the position.

4. **Slippage Protection**: Always set reasonable minimum amounts when decreasing liquidity or swapping to protect against slippage.

5. **SOL Handling**: Set `useSOLBalance: true` to automatically handle native SOL wrapping/unwrapping.