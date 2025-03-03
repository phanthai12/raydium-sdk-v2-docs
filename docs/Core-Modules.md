# Core Modules Documentation

This document describes the core modules and interfaces of the Raydium SDK V2.

## Raydium

The main entry point for the Raydium SDK V2.

```typescript
interface Raydium {
  // Properties
  cluster: Cluster;                   // Current Solana cluster (mainnet, devnet, testnet, localnet)
  token: TokenModule;                 // Token management module
  account: Account;                   // Account management module
  api: Api;                           // API interaction module
  clmm: Clmm;                         // Concentrated Liquidity Market Maker module
  cpmm: Cpmm;                         // Constant Product Market Maker module
  farm: Farm;                         // Farm interaction module
  liquidity: Liquidity;               // Liquidity management module
  tradeV2: TradeV2;                   // Trading functionality module

  // Methods
  static load(config: RaydiumLoadParams): Promise<Raydium>;  // Initialize the SDK with configuration
  checkOwner(): void;                                        // Verify owner permissions
  fetchChainTime(): Promise<void>;                           // Fetch current blockchain time
  fetchV3TokenList(forceUpdate?: boolean): Promise<ApiV3TokenRes>; // Fetch token list from API
}
```

### RaydiumLoadParams

Configuration parameters for initializing the Raydium SDK.

```typescript
interface RaydiumLoadParams {
  connection: Connection;                 // Solana connection instance
  owner: Keypair | PublicKey;             // Owner wallet (keypair or public key)
  cluster?: Cluster;                      // Target cluster (mainnet, devnet, testnet, localnet)
  signAllTransactions?: SignAllTransactions; // Function to sign multiple transactions
  tokenAccounts?: TokenAccount[];         // Pre-existing token account information
  tokenAccountRowInfos?: any[];           // Token account raw information
  disableLoadToken?: boolean;             // Disable token information loading
  apiRequestInterval?: number;            // Interval between API requests
  apiRequestTimeout?: number;             // API request timeout duration
  urlConfigs?: ApiUrlConfig;              // API URL configuration
  jupTokenType?: JupTokenType;            // Jupiter token type preferences
}
```

## TokenModule

Module for managing token information and operations.

```typescript
interface TokenModule {
  // Private properties
  _tokenList: TokenInfo[];                       // List of token information
  _tokenMap: Map<string, TokenInfo>;             // Map of token addresses to information
  _blackTokenMap: Set<string>;                   // Set of blacklisted token addresses
  _mintGroup: Object;                            // Token grouping by mint

  // Methods
  load(params?: LoadParams): Promise<void>;      // Load token information
  getTokenInfo(mint: string|PublicKey): Promise<ApiV3Token>; // Get specific token information
}
```

### LoadParams

Parameters for loading token information.

```typescript
interface LoadParams {
  forceCache?: boolean;                  // Force using cached token list
  forceRefresh?: boolean;                // Force refresh from API
}
```

## MakeTxData

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

## Configuration Types

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

## Initialization Example

```typescript
// Initialize the SDK
const raydium = await Raydium.load({
  connection: new Connection('https://api.mainnet-beta.solana.com'),
  owner: wallet, // Keypair or PublicKey
  cluster: Cluster.MAINNET,
  // Optional parameters
  disableLoadToken: false,
  apiRequestInterval: 1000,
  apiRequestTimeout: 15000
});
```