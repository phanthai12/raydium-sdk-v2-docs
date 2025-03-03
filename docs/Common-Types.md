# Common Types and Utilities Documentation

This document describes the common types, interfaces, and utilities used across various modules in the Raydium SDK V2.

## Transaction Types

### MakeTxData

Interface for transaction data returned by most operations.

```typescript
interface MakeTxData {
  // Properties
  address: any;                // Related addresses
  innerTransactions: any[];    // Inner transactions

  // Optional property
  mockPoolInfo?: any;          // Mock pool information for simulation

  // Methods
  toTransaction(): Promise<{
    transaction: Transaction,
    signers: Signer[]
  }>;                          // Convert to Legacy transaction
  
  toVersionedTransaction(): Promise<{
    transaction: VersionedTransaction,
    signers: Signer[]
  }>;                          // Convert to Versioned transaction
}
```

### TxVersion

Transaction version enum.

```typescript
enum TxVersion {
  LEGACY = "legacy",   // Legacy transaction format
  V0 = "v0"            // Version 0 transaction format
}
```

### ComputeBudgetConfig

Configuration for transaction compute budget.

```typescript
interface ComputeBudgetConfig {
  units?: number;         // Compute units
  microLamports?: number; // Priority fee in micro-lamports
}
```

### TxTipConfig

Configuration for transaction tip/priority fee.

```typescript
interface TxTipConfig {
  lamports: number;       // Tip amount in lamports
}
```

## Environment Types

### Cluster

Enum for Solana cluster.

```typescript
enum Cluster {
  MAINNET = 'mainnet-beta',    // Mainnet
  DEVNET = 'devnet',           // Devnet
  TESTNET = 'testnet',         // Testnet
  LOCALNET = 'localnet'        // Local development network
}
```

## Pool Types

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

## Token Types

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

### ApiV3Token

Token information from the API.

```typescript
interface ApiV3Token {
  chain: string;               // Blockchain (solana)
  address: string;             // Token mint address
  name: string;                // Token name
  symbol: string;              // Token symbol
  decimals: number;            // Token decimals
  logoURI?: string;            // Token logo URL
  tags?: string[];             // Token tags
  extensions?: {               // Additional token information
    coingeckoId?: string;      // Coingecko ID
    website?: string;          // Website
    twitter?: string;          // Twitter handle
  };
  whitelisted?: boolean;       // Whether token is whitelisted
  listed?: boolean;            // Whether token is listed
}
```

## Farm Types

### FarmInfo

Information about a farm.

```typescript
interface FarmInfo {
  id: string;                   // Farm ID
  programId: PublicKey;         // Farm program ID
  status: FarmStatus;           // Farm status
  lpMint: PublicKey;            // LP token mint
  rewardInfos: RewardInfo[];    // Reward information array
  version: number;              // Farm version
  authority: PublicKey;         // Farm authority
  lpVault: PublicKey;           // LP token vault
  rewardVaults: PublicKey[];    // Reward token vaults
}
```

### FarmStatus

Enum for farm status.

```typescript
enum FarmStatus {
  UPCOMING = 'upcoming',      // Farm not yet active
  ACTIVE = 'active',          // Farm currently active
  ENDED = 'ended'             // Farm period ended
}
```

### RewardInfo

Information about farm rewards.

```typescript
interface RewardInfo {
  mint: PublicKey;             // Reward token mint
  vault: PublicKey;            // Reward token vault
  rewardPerSecond: BN;         // Reward rate per second
  rewardOpenTime: number;      // Reward period start time
  rewardEndTime: number;       // Reward period end time
  rewardType: RewardType;      // Reward type
}
```

### RewardType

Enum for reward token type.

```typescript
enum RewardType {
  Standard = 0,               // Standard SPL token
  Native = 1                  // Native SOL
}
```

## Trade Types

### SwapMode

Enum for swap mode.

```typescript
enum SwapMode {
  ExactIn = 'ExactIn',            // Exact input amount
  ExactOut = 'ExactOut'           // Exact output amount
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

## API URL Configuration

### ApiUrlConfig

Configuration for API URLs.

```typescript
interface ApiUrlConfig {
  api?: string;                  // Main API URL
  farmApi?: string;              // Farm API URL
  poolApi?: string;              // Pool API URL
  tokenApi?: string;             // Token API URL
  quoteApi?: string;             // Quote API URL
}
```

## Working with Libraries

### BN (Big Number)

Raydium SDK uses the `bn.js` library for handling large numbers, especially when dealing with token amounts.

```typescript
// Import
import BN from 'bn.js';

// Create a BN
const amount = new BN('1000000000'); // 1 SOL in lamports

// Basic operations
const doubled = amount.mul(new BN(2));
const halved = amount.div(new BN(2));
```

### Decimal

For handling decimal values like prices, the SDK uses the `decimal.js` library.

```typescript
// Import
import Decimal from 'decimal.js';

// Create a Decimal
const price = new Decimal('10.5');

// Basic operations
const multiplied = price.mul(2);
const percentIncrease = price.mul(1.05);
```

## Important Notes on Using Common Types

1. **Transaction Versions**: Different Solana RPC endpoints may have different support for transaction versions. Use `TxVersion.LEGACY` for maximum compatibility or `TxVersion.V0` for newer features like address lookup tables.

2. **Big Numbers**: Always use BN for token amounts to avoid precision issues with JavaScript numbers.

3. **Decimals and Prices**: Use Decimal for price calculations to maintain precision.

4. **Public Keys**: When converting between string and PublicKey, use `new PublicKey(stringKey)` and `publicKey.toString()`.

5. **Cluster Selection**: The SDK's behavior may vary slightly depending on the selected cluster. Some features might only be available on mainnet.

6. **Token Precision**: Be mindful of token decimal places when calculating amounts. For example, USDC has 6 decimals, while most tokens have 9.

7. **Transaction Signing**: When using `MakeTxData`, remember to sign the transaction with the appropriate signer(s) before sending it.

8. **Compute Budget**: For complex operations, consider setting appropriate compute budget to avoid transaction failures.
