# Liquidity and Trade Documentation

This document covers the Liquidity and TradeV2 modules which provide functionality for managing liquidity and executing trades on Raydium.

## Liquidity Interface

The Liquidity module provides functionality for managing liquidity in traditional Raydium pools.

```typescript
interface Liquidity {
  // Add liquidity to a pool
  addLiquidity(props: AddLiquidityParams): Promise<MakeTxData>;
  
  // Remove liquidity from a pool
  removeLiquidity(props: RemoveLiquidityParams): Promise<MakeTxData>;
  
  // Get LP token balance
  getLpBalance(props: GetLpBalanceParams): Promise<BN>;
}
```

### AddLiquidityParams

Parameters for adding liquidity to a pool.

```typescript
interface AddLiquidityParams {
  poolInfo: PoolInfo;             // Pool information
  amountA: BN;                    // Token A amount to add
  amountB: BN;                    // Token B amount to add
  fixedSide: 'a' | 'b';           // Which token amount is fixed
  slippage: number;               // Slippage tolerance percentage
  ownerInfo: {                    // Owner information
    feePayer: PublicKey;          // Fee payer
    useSOLBalance?: boolean;      // Use SOL instead of wSOL
  };
  txVersion: TxVersion;           // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;      // Transaction tip configuration (optional)
}
```

### RemoveLiquidityParams

Parameters for removing liquidity from a pool.

```typescript
interface RemoveLiquidityParams {
  poolInfo: PoolInfo;             // Pool information
  lpAmount: BN;                   // LP token amount to burn
  amountMinA: BN;                 // Minimum token A to receive
  amountMinB: BN;                 // Minimum token B to receive
  ownerInfo: {                    // Owner information
    feePayer: PublicKey;          // Fee payer
    useSOLBalance?: boolean;      // Use SOL instead of wSOL
  };
  txVersion: TxVersion;           // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;      // Transaction tip configuration (optional)
}
```

### GetLpBalanceParams

Parameters for getting LP token balance.

```typescript
interface GetLpBalanceParams {
  poolInfo: PoolInfo;             // Pool information
  owner: PublicKey;               // Token owner
  connection: Connection;         // Solana connection
}
```

## TradeV2 Interface

The TradeV2 module provides functionality for token swapping and trading.

```typescript
interface TradeV2 {
  // Swap tokens in a single pool
  swap(props: SwapParams): Promise<MakeTxData>;
  
  // Swap tokens using routing through multiple pools
  routeSwap(props: RouteSwapParams): Promise<MakeTxData>;
  
  // Get quote price for a swap
  getQuotePrice(props: QuotePriceParams): Promise<QuoteResult>;
}
```

### SwapParams

Parameters for swapping tokens in a single pool.

```typescript
interface SwapParams {
  poolInfo: PoolInfo;               // Pool information
  inputMint: PublicKey;             // Input token mint
  outputMint: PublicKey;            // Output token mint
  amountIn: BN;                     // Input amount
  minAmountOut: BN;                 // Minimum output amount
  ownerInfo: {                      // Owner information
    feePayer: PublicKey;            // Fee payer
    useSOLBalance?: boolean;        // Use SOL instead of wSOL
  };
  txVersion: TxVersion;             // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;        // Transaction tip configuration (optional)
}
```

### RouteSwapParams

Parameters for swapping tokens using routing.

```typescript
interface RouteSwapParams {
  routeInfo: RouteInfo;             // Route information
  inputMint: PublicKey;             // Input token mint
  outputMint: PublicKey;            // Output token mint
  amountIn: BN;                     // Input amount
  minAmountOut: BN;                 // Minimum output amount
  ownerInfo: {                      // Owner information
    feePayer: PublicKey;            // Fee payer
    useSOLBalance?: boolean;        // Use SOL instead of wSOL
  };
  txVersion: TxVersion;             // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;        // Transaction tip configuration (optional)
}
```

### QuotePriceParams

Parameters for getting a swap quote.

```typescript
interface QuotePriceParams {
  inputMint: PublicKey;             // Input token mint
  outputMint: PublicKey;            // Output token mint
  amount: BN;                       // Amount to swap
  swapMode: SwapMode;               // Swap mode (ExactIn or ExactOut)
  slippage: number;                 // Slippage tolerance percentage
  onlyDirectRoute?: boolean;        // Use only direct routes (optional)
  excludeDexes?: string[];          // Dexes to exclude (optional)
  maxRouteCount?: number;           // Maximum number of routes (optional)
}
```

### QuoteResult

Result of a quote price request.

```typescript
interface QuoteResult {
  inputMint: PublicKey;            // Input token mint
  outputMint: PublicKey;           // Output token mint
  inputAmount: BN;                 // Input amount
  outputAmount: BN;                // Output amount
  otherAmountThreshold: BN;        // Other amount threshold
  swapMode: SwapMode;              // Swap mode
  fee: TokenFee;                   // Fee information
  routePlan: RoutePlan[];          // Route plan
  slippageBps: number;             // Slippage in basis points
  priceImpactPct: number;          // Price impact percentage
}
```

## Data Interfaces

### PoolInfo

Information about a liquidity pool.

```typescript
interface PoolInfo {
  id: string;                      // Pool ID
  baseMint: string;                // Base token mint
  quoteMint: string;               // Quote token mint
  lpMint: string;                  // LP token mint
  baseDecimals: number;            // Base token decimals
  quoteDecimals: number;           // Quote token decimals
  lpDecimals: number;              // LP token decimals
  version: number;                 // Pool version
  programId: string;               // Program ID
  authority: string;               // Pool authority
  baseVault: string;               // Base token vault
  quoteVault: string;              // Quote token vault
  feeRate: number;                 // Fee rate
  type: PoolType;                  // Pool type
}
```

### PoolType

Enum for pool type.

```typescript
enum PoolType {
  STANDARD = 'standard',          // Standard Raydium pool
  STABLE = 'stable',              // Stable swap pool
  CLMM = 'clmm'                   // Concentrated liquidity pool
}
```

### RouteInfo

Information about a swap route.

```typescript
interface RouteInfo {
  routePlan: RoutePlan[];         // Route execution plan
  amountIn: BN;                   // Input amount
  amountOut: BN;                  // Expected output amount
  fee: TokenFee;                  // Fee information
  priceImpactPct: number;         // Price impact percentage
}
```

### RoutePlan

Information about a single step in a route.

```typescript
interface RoutePlan {
  percent: number;                // Percentage of input amount to route
  inputMint: PublicKey;           // Input token mint
  outputMint: PublicKey;          // Output token mint
  poolId: PublicKey;              // Pool ID to use
  poolType: PoolType;             // Type of pool
}
```

### TokenFee

Information about token swap fees.

```typescript
interface TokenFee {
  amount: BN;                     // Fee amount
  mint: PublicKey;                // Fee token mint
  percent: number;                // Fee percentage of trade
}
```

### SwapMode

Enum for swap mode.

```typescript
enum SwapMode {
  ExactIn = 'ExactIn',            // Exact input amount
  ExactOut = 'ExactOut'           // Exact output amount
}
```

## Notes on Liquidity Management

1. **LP Tokens**: When adding liquidity, you receive LP tokens representing your share of the pool. These tokens can be redeemed later to withdraw your share.

2. **Fixed Side**: When adding liquidity with `fixedSide`, the amount of the fixed token will remain constant, and the amount of the other token will be calculated based on the current pool ratio.

3. **Slippage Tolerance**: Always set a reasonable slippage tolerance when adding or removing liquidity to account for price movements.

4. **Minimum Amounts**: When removing liquidity, set reasonable minimum amounts to protect against unfavorable price movements.

## Notes on Trading

1. **Route Swapping**: For better execution prices, the `routeSwap` method can find and execute trades across multiple pools.

2. **Price Impact**: Always check the `priceImpactPct` from quote results to understand how your trade will affect the market price.

3. **Swap Modes**:
   - `ExactIn`: You specify the exact input amount, and get at least the minimum output amount
   - `ExactOut`: You specify the exact output amount, and use at most the maximum input amount

4. **Quote First**: Always get a quote first with `getQuotePrice` before executing a swap to understand expected amounts and price impact.

5. **Maximum Routes**: For large trades, consider increasing `maxRouteCount` to find more optimal routes, although this will increase computation time.