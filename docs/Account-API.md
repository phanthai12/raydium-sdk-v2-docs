# Account and API Documentation

This document covers the Account and API modules of the Raydium SDK V2, which handle wallet accounts, token accounts, and communication with Raydium's API services.

## Account Interface

The Account module manages wallet accounts and token accounts for the Raydium SDK.

```typescript
interface Account {
  // Properties
  tokenAccounts: Map<string, PublicKey>;      // Map of token mint to account address
  tokenAccountRawInfos: any[];                // Raw token account information

  // Methods
  fetchWalletTokenAccounts(): Promise<void>;  // Fetch token accounts for the connected wallet
  
  // Get or create a token account for a specific mint
  getOrCreateTokenAccount(params: GetOrCreateTokenAccountParams): Promise<TokenAccountResult>;
  
  // Reset token account state
  resetTokenAccounts(): void;
}
```

### GetOrCreateTokenAccountParams

Parameters for getting or creating a token account.

```typescript
interface GetOrCreateTokenAccountParams {
  mint: PublicKey;                // Token mint
  owner: PublicKey;               // Account owner
  createInfo?: {                  // Parameters for creating if needed
    connection: Connection;       // Solana connection
    payer: PublicKey;             // Fee payer
    systemProgram: PublicKey;     // System program ID 
    tokenProgram: PublicKey;      // Token program ID
    associatedTokenProgram: PublicKey; // Associated token program ID
  };
  skipCreateCheck?: boolean;      // Skip checking if account exists first
  checkCache?: boolean;           // Check cached token accounts
}
```

### TokenAccountResult

Result of getting or creating a token account.

```typescript
interface TokenAccountResult {
  tokenAccount: PublicKey;        // Token account address
  isCreated: boolean;             // Whether the account was newly created
  transactionPayload?: {          // Transaction data if created
    transaction: Transaction;
    signers: Signer[];
  };
}
```

## API Interface

The API module handles communication with the Raydium API servers.

```typescript
interface Api {
  // Properties
  cluster: Cluster;               // Current Solana cluster
  api: AxiosInstance;             // Axios instance for API requests

  // Methods
  
  // Get list of supported tokens
  getTokenList(): Promise<ApiV3TokenListResponse>;
  
  // Get information for specific tokens
  getTokenInfo(mint: string[]): Promise<ApiV3Token[]>;
  
  // Get list of liquidity pools
  getPoolList(props: GetPoolListParams): Promise<PoolsApiReturn>;
  
  // Fetch pool information by ID
  fetchPoolById(props: FetchPoolByIdParams): Promise<ApiV3PoolInfoItem[]>;
  
  // Fetch pools by token mints
  fetchPoolByMints(props: FetchPoolByMintsParams): Promise<PoolsApiReturn>;
  
  // Fetch farm information by ID
  fetchFarmInfoById(props: FetchFarmInfoByIdParams): Promise<FormatFarmInfoOut[]>;
  
  // Fetch farm keys by ID
  fetchFarmKeysById(props: FetchFarmKeysByIdParams): Promise<FormatFarmKeyOut[]>;
}
```

### GetPoolListParams

Parameters for getting a list of pools.

```typescript
interface GetPoolListParams {
  type?: "all" | "clmm" | "standard" | "stable" | "stable-secondary"; // Pool type filter
  sort?: "liquidity" | "volume" | "default"; // Sort order
  order?: "asc" | "desc";       // Sort direction
  page?: number;                // Page number (pagination)
  pageSize?: number;            // Number of results per page
}
```

### FetchPoolByIdParams

Parameters for fetching pools by ID.

```typescript
interface FetchPoolByIdParams {
  ids: string;                 // Comma-separated pool IDs
}
```

### FetchPoolByMintsParams

Parameters for fetching pools by token mints.

```typescript
interface FetchPoolByMintsParams {
  mint1: string;               // First token mint
  mint2: string;               // Second token mint
  type?: string;               // Pool type filter
  sort?: string;               // Sort order
  order?: string;              // Sort direction
  page?: number;               // Page number (pagination)
}
```

### FetchFarmInfoByIdParams

Parameters for fetching farm information by ID.

```typescript
interface FetchFarmInfoByIdParams {
  ids: string;                 // Comma-separated farm IDs
}
```

### FetchFarmKeysByIdParams

Parameters for fetching farm keys by ID.

```typescript
interface FetchFarmKeysByIdParams {
  ids: string;                 // Comma-separated farm IDs
}
```

## API Response Interfaces

### ApiV3TokenListResponse

Response for token list API request.

```typescript
interface ApiV3TokenListResponse {
  official: ApiV3Token[];      // Official tokens
  unOfficial: ApiV3Token[];    // Unofficial tokens
  blacklist: string[];         // Blacklisted token mints
  timestamp: number;           // Response timestamp
  version: {                   // API version information
    major: number;
    minor: number;
    patch: number;
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

### PoolsApiReturn

Pool list API response.

```typescript
interface PoolsApiReturn {
  data: ApiV3PoolInfoItem[];   // Pool information items
  total: number;               // Total number of pools matching criteria
  success: boolean;            // Success status
}
```

### ApiV3PoolInfoItem

Pool information from the API.

```typescript
interface ApiV3PoolInfoItem {
  id: string;                  // Pool ID
  mintA: {                     // First token information
    address: string;           // Token address
    decimals: number;          // Token decimals
    symbol?: string;           // Token symbol
    name?: string;             // Token name
  };
  mintB: {                     // Second token information
    address: string;           // Token address
    decimals: number;          // Token decimals
    symbol?: string;           // Token symbol
    name?: string;             // Token name
  };
  vaultA: string;              // Vault for token A
  vaultB: string;              // Vault for token B
  lpMint: string;              // LP token mint
  programId: string;           // Program ID
  authority: string;           // Pool authority
  type: string;                // Pool type
  version: number;             // Pool version
  fee: number;                 // Fee percentage
  liquidity: {                 // Liquidity information
    amountA: string;           // Token A amount
    amountB: string;           // Token B amount
    lpSupply: string;          // LP token supply
    marketPrice?: string;      // Market price
  };
  volume: {                    // Volume information
    day: string;               // 24h volume
    week: string;              // 7d volume
    month: string;             // 30d volume
  };
  apr: {                       // APR information
    day: string;               // 24h APR
    week: string;              // 7d APR
    month: string;             // 30d APR
  };
  price: {                     // Price information
    amountA: string;           // Price in terms of token A
    amountB: string;           // Price in terms of token B
  };
}
```

### FormatFarmInfoOut

Farm information from the API.

```typescript
interface FormatFarmInfoOut {
  id: string;                  // Farm ID
  lpMint: string;              // LP token mint
  lpVault: string;             // LP token vault
  rewardInfos: {               // Reward information
    rewardMint: string;        // Reward token mint
    rewardVault: string;       // Reward token vault
    perSecond: string;         // Reward per second
    openTime: number;          // Reward period start time
    endTime: number;           // Reward period end time
    rewardType: number;        // Reward type
  }[];
  version: number;             // Farm version
  programId: string;           // Program ID
  authority: string;           // Farm authority
  poolId: string;              // Associated pool ID
  poolType: string;            // Associated pool type
  lpPrice: string;             // LP token price
  lpSupply: string;            // LP token supply
  mintA: string;               // Token A mint
  mintB: string;               // Token B mint
}
```

### FormatFarmKeyOut

Farm keys from the API.

```typescript
interface FormatFarmKeyOut {
  id: string;                  // Farm ID
  programId: string;           // Program ID
  lpMint: string;              // LP token mint
  authority: string;           // Farm authority
  lpVault: string;             // LP token vault
  rewardInfos: {               // Reward key information
    rewardMint: string;        // Reward token mint
    rewardVault: string;       // Reward token vault
  }[];
}
```

## Notes on Account Management

1. **Token Account Caching**: The Account module maintains a cache of token accounts to avoid redundant fetches. Use `resetTokenAccounts()` to clear this cache if needed.

2. **Associated Token Accounts**: The `getOrCreateTokenAccount` method automatically uses associated token accounts (ATA) by default, which is the recommended token account type.

3. **Token Account Creation**: When using `getOrCreateTokenAccount`, you can either check if an account exists first (default behavior) or skip this check with `skipCreateCheck: true` for better performance when you know the account doesn't exist.

4. **SOL Handling**: Native SOL is wrapped as wSOL (Wrapped SOL) automatically when needed for operations that require SPL tokens.

## Notes on API Usage

1. **Rate Limiting**: Be mindful of rate limits when making multiple API requests in succession. Use `apiRequestInterval` in the SDK initialization to control request timing.

2. **Data Caching**: The API module includes caching mechanisms to reduce redundant requests. Use the `forceRefresh` option when needed to bypass cache.

3. **Pagination**: For methods that support pagination, use the `page` and `pageSize` parameters to manage result sets, especially for large data sets.

4. **Error Handling**: API requests may fail due to network issues or server-side errors. Always implement proper error handling when using these methods.

5. **Response Processing**: API responses are typically returned in standardized formats, but may require additional processing to convert string values to appropriate types (numbers, BN, etc.).
