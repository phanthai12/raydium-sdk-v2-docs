# Farm Documentation

The Farm module provides functionality for interacting with yield farming on Raydium. It allows users to deposit LP tokens, withdraw them, and harvest rewards.

## Farm Interface

The main interface for interacting with farm functionality.

```typescript
interface Farm {
  // Deposit LP tokens into a farm
  deposit(props: DepositParams): Promise<MakeTxData>;
  
  // Withdraw LP tokens from a farm
  withdraw(props: WithdrawParams): Promise<MakeTxData>;
  
  // Harvest rewards from a farm
  harvest(props: HarvestParams): Promise<MakeTxData>;
  
  // Fetch user's farm positions
  fetchFarmPosition(props: FetchFarmPositionParams): Promise<FarmPosition[]>;
}
```

## Farm Parameter Interfaces

### DepositParams

Parameters for depositing LP tokens into a farm.

```typescript
interface DepositParams {
  farmInfo: FarmInfo;           // Farm information
  lpAmount: BN;                 // LP token amount to deposit
  ownerInfo: {                  // Owner information
    feePayer: PublicKey;        // Fee payer
    useSOLBalance?: boolean;    // Use SOL instead of wSOL
  };
  txVersion: TxVersion;         // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;    // Transaction tip configuration (optional)
}
```

### WithdrawParams

Parameters for withdrawing LP tokens from a farm.

```typescript
interface WithdrawParams {
  farmInfo: FarmInfo;           // Farm information
  lpAmount: BN;                 // LP token amount to withdraw
  withdrawAll?: boolean;        // Withdraw all LP tokens (optional)
  ownerInfo: {                  // Owner information
    feePayer: PublicKey;        // Fee payer
    useSOLBalance?: boolean;    // Use SOL instead of wSOL
  };
  txVersion: TxVersion;         // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;    // Transaction tip configuration (optional)
}
```

### HarvestParams

Parameters for harvesting rewards from a farm.

```typescript
interface HarvestParams {
  farmInfo: FarmInfo;           // Farm information
  ownerInfo: {                  // Owner information
    feePayer: PublicKey;        // Fee payer
    useSOLBalance?: boolean;    // Use SOL instead of wSOL
  };
  txVersion: TxVersion;         // Transaction version
  computeBudgetConfig?: ComputeBudgetConfig; // Compute budget configuration (optional)
  txTipConfig?: TxTipConfig;    // Transaction tip configuration (optional)
}
```

### FetchFarmPositionParams

Parameters for fetching farm positions.

```typescript
interface FetchFarmPositionParams {
  farmInfos: FarmInfo[];        // Array of farm information
  connection: Connection;       // Solana connection
  owner: PublicKey;             // Owner public key
  useCache?: boolean;           // Use cached results (optional)
}
```

## Farm Data Interfaces

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

### FarmPosition

Information about a user's farm position.

```typescript
interface FarmPosition {
  farmId: string;              // Farm ID
  owner: PublicKey;            // Position owner
  lpAmount: BN;                // LP token amount deposited
  rewardDebts: BN[];           // Reward debts
  rewardAmounts: BN[];         // Claimable reward amounts
  depositedTime: number;       // Time of deposit
  lastUpdateTime: number;      // Last position update time
}
```

## Notes on Using Farm

1. **Farm Types**: Raydium supports farms for both CPMM and CLMM pools. For CLMM pools, you'll need to lock your position NFT rather than depositing LP tokens directly.

2. **Farm Status**: Always check the farm status before interacting with it. Deposits may be restricted for farms in `UPCOMING` or `ENDED` status.

3. **Rewards Accrual**: Rewards accrue continuously based on the `rewardPerSecond` rate. The actual amount depends on your share of the total staked LP tokens.

4. **Multiple Rewards**: A single farm can offer multiple reward tokens simultaneously. Each reward token has its own parameters and schedule.

5. **Harvesting Frequency**: For optimal gas efficiency, consider harvesting rewards less frequently, especially if reward amounts are small.

6. **LP Token Requirements**: Before depositing into a farm, you need to have acquired LP tokens by providing liquidity to the corresponding pool.

7. **Reward Calculation**: Reward accrual follows this formula:
   ```
   pendingReward = (userLpAmount * accumulatedRewardPerShare) - userRewardDebt
   ```

8. **CLMM Position Farming**: For CLMM pools, use the `lockPosition` and `harvestLockPosition` methods from the CLMM module instead of the Farm module's methods.