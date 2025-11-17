# PolkaMesh Architecture Documentation

> **Comprehensive technical architecture for the PolkaMesh decentralized AI compute and data marketplace**

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Principles](#architecture-principles)
3. [Component Architecture](#component-architecture)
4. [Smart Contract Layer](#smart-contract-layer)
5. [SDK Layer](#sdk-layer)
6. [Data Flow & Interactions](#data-flow--interactions)
7. [Security Architecture](#security-architecture)
8. [Scalability & Performance](#scalability--performance)
9. [Integration Patterns](#integration-patterns)
10. [Deployment Architecture](#deployment-architecture)

---

## System Overview

PolkaMesh is a multi-layered decentralized application built on Polkadot that creates a trustless marketplace for AI compute resources and IoT data. The system enables job submitters, compute providers, and data owners to interact through smart contracts without intermediaries.

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Presentation Layer                          │
│                                                                   │
│   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│   │   Web UI    │  │Mobile Apps  │  │  CLI Tool   │            │
│   │(React/Next) │  │(React Native│  │  (Node.js)  │            │
│   └──────┬──────┘  └──────┬──────┘  └──────┬──────┘            │
│          │                 │                 │                   │
└──────────┼─────────────────┼─────────────────┼───────────────────┘
           │                 │                 │
           └─────────────────┴─────────────────┘
                             │
┌────────────────────────────┼────────────────────────────────────┐
│                      Application Layer                           │
│                             │                                    │
│         ┌──────────────────────────────────┐                    │
│         │     PolkaMesh TypeScript SDK      │                   │
│         │  ┌────────────────────────────┐  │                   │
│         │  │  Contract Wrapper Classes  │  │                   │
│         │  └────────────────────────────┘  │                   │
│         │  ┌────────────────────────────┐  │                   │
│         │  │   Utilities & Helpers      │  │                   │
│         │  └────────────────────────────┘  │                   │
│         │  ┌────────────────────────────┐  │                   │
│         │  │   Encryption & Security    │  │                   │
│         │  └────────────────────────────┘  │                   │
│         └──────────┬───────────────────────┘                   │
│                    │                                            │
└────────────────────┼────────────────────────────────────────────┘
                     │
                     │ Polkadot.js API
                     │
┌────────────────────┼────────────────────────────────────────────┐
│              Blockchain Layer (Polkadot)                         │
│                    │                                            │
│    ┌───────────────┴───────────────┐                           │
│    │   Paseo Pop Network (Testnet) │                           │
│    └───────────────┬───────────────┘                           │
│                    │                                            │
│  ┌─────────────────┼─────────────────┐                         │
│  │                 │                 │                         │
│  ▼                 ▼                 ▼                         │
│ ┌──────┐      ┌──────┐         ┌──────┐                       │
│ │ Job  │      │Escrow│         │Provider│                     │
│ │Queue │◄────►│      │◄───────►│Registry│                     │
│ └──┬───┘      └──┬───┘         └───┬────┘                     │
│    │             │                 │                           │
│    └─────────────┴─────────────────┘                           │
│                  │                                             │
│    ┌─────────────┼─────────────────┐                          │
│    │             │                 │                           │
│    ▼             ▼                 ▼                           │
│ ┌──────┐    ┌────────┐       ┌──────────┐                     │
│ │ Data │    │  MEV   │       │  Phala   │                     │
│ │ NFT  │    │Protect │       │Processor │                     │
│ └──────┘    └────────┘       └────┬─────┘                     │
│                                    │                           │
└────────────────────────────────────┼───────────────────────────┘
                                     │
┌────────────────────────────────────┼───────────────────────────┐
│              Off-Chain Layer       │                           │
│                                    ▼                           │
│                          ┌──────────────────┐                  │
│                          │  Phala Network   │                  │
│                          │                  │                  │
│                          │  ┌────────────┐  │                  │
│                          │  │ Phat       │  │                  │
│                          │  │ Contract   │  │                  │
│                          │  └─────┬──────┘  │                  │
│                          │        │         │                  │
│                          │        ▼         │                  │
│                          │  ┌────────────┐  │                  │
│                          │  │TEE Executor│  │                  │
│                          │  │(SGX/TDX)   │  │                  │
│                          │  └────────────┘  │                  │
│                          └──────────────────┘                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Architecture Principles

### 1. **Modularity**

Each smart contract is independently deployable and upgradeable, following single responsibility principle:

- **AIJobQueue**: Job lifecycle management only
- **PaymentEscrow**: Payment handling only
- **ComputeProviderRegistry**: Provider management only
- **DataNFTRegistry**: Data tokenization only
- **MEVProtection**: Transaction analysis only
- **PhalaJobProcessor**: TEE coordination only

**Benefits**:
- Easier testing and debugging
- Independent upgrades without affecting other components
- Reduced contract size for gas optimization
- Clear separation of concerns

### 2. **Decentralization**

No single point of failure or control:

- **No admin keys**: Contracts are immutable after deployment
- **Distributed compute**: Any provider can participate
- **Permissionless**: No KYC or whitelisting required
- **Transparent**: All transactions on-chain and auditable

### 3. **Security-First**

Multi-layered security approach:

- **Access Control**: Function-level authorization checks
- **Input Validation**: Comprehensive parameter validation
- **Reentrancy Protection**: Guards against reentrancy attacks
- **Overflow Protection**: Safe math operations
- **Error Handling**: Fail-safe error propagation

### 4. **Type Safety**

Comprehensive type system throughout the stack:

- **SDK**: 250+ TypeScript type definitions
- **Contracts**: Rust's strong type system with ink! macros
- **API**: Typed contract interfaces via Polkadot.js

### 5. **Privacy-Preserving**

Data privacy at every layer:

- **On-chain**: Only hashes and metadata stored
- **Off-chain**: Encrypted payloads with ECIES
- **TEE**: Confidential compute in Phala Network
- **Access Control**: Fine-grained permissions

---

## Component Architecture

### Layer 1: Smart Contracts (On-Chain)

#### AI Job Queue Contract

**Responsibilities**:
- Accept job submissions with parameters
- Manage job lifecycle state machine
- Emit events for off-chain monitoring
- Validate job parameters

**State Machine**:
```
Registered ──┐
             │
             ├──► Assigned ──┐
             │               │
             │               ├──► InProgress ──┐
             │               │                 │
             ▼               ▼                 ├──► Completed
          Cancelled                            │
                                               ├──► Disputed
                                               │
                                               ▼
                                            Failed
```

**Data Structures**:
```rust
pub struct Job {
    id: u64,
    submitter: AccountId,
    provider: Option<AccountId>,
    description: String,
    budget: Balance,
    data_set_id: Option<u64>,
    compute_type: ComputeType,
    estimated_runtime: u64,
    status: JobStatus,
    submitted_at: Timestamp,
    result_hash: Option<Hash>,
}

pub enum JobStatus {
    Registered,
    Assigned,
    InProgress,
    Completed,
    Disputed,
    Cancelled,
    Failed,
}

pub enum ComputeType {
    CPU,
    GPU,
    TPU,
    PhalaTEE,
    Acurast,
    Custom(String),
}
```

**Key Functions**:
```rust
// Submit new job
#[ink(message, payable)]
pub fn submit_job(
    &mut self,
    description: String,
    budget: Balance,
    data_set_id: Option<u64>,
    compute_type: ComputeType,
    estimated_runtime: u64,
) -> Result<u64, Error>

// Assign job to provider
#[ink(message)]
pub fn assign_provider(
    &mut self,
    job_id: u64,
    provider: AccountId,
) -> Result<(), Error>

// Update job status
#[ink(message)]
pub fn mark_in_progress(&mut self, job_id: u64) -> Result<(), Error>

#[ink(message)]
pub fn mark_completed(
    &mut self,
    job_id: u64,
    result_hash: Hash,
) -> Result<(), Error>
```

---

#### Payment Escrow Contract

**Responsibilities**:
- Lock funds when jobs are submitted
- Release funds upon job completion
- Handle refunds for cancellations/disputes
- Support multi-token payments

**Data Structures**:
```rust
pub struct Escrow {
    job_id: u64,
    submitter: AccountId,
    provider: Option<AccountId>,
    amount: Balance,
    status: EscrowStatus,
    deposited_at: Timestamp,
    released_at: Option<Timestamp>,
}

pub enum EscrowStatus {
    Locked,
    Released,
    Refunded,
    Disputed,
}
```

**Payment Flow**:
```
1. Job submitted
   ↓
2. Funds deposited to escrow (locked)
   ↓
3. Provider assigned
   ↓
4. Job completed
   ↓
5. Verification period (24 hours)
   ↓
6. Funds released to provider
```

**Key Functions**:
```rust
#[ink(message, payable)]
pub fn deposit_for_job(
    &mut self,
    job_id: u64,
) -> Result<(), Error>

#[ink(message)]
pub fn release_to_provider(
    &mut self,
    job_id: u64,
) -> Result<(), Error>

#[ink(message)]
pub fn refund_to_submitter(
    &mut self,
    job_id: u64,
) -> Result<(), Error>
```

---

#### Compute Provider Registry Contract

**Responsibilities**:
- Provider registration and onboarding
- Capability and profile management
- Reputation tracking and scoring
- Bidding system for job assignment

**Data Structures**:
```rust
pub struct Provider {
    address: AccountId,
    endpoint: String,
    capabilities: Vec<ComputeType>,
    stake: Balance,
    hourly_rate: Balance,
    reputation_score: u32,
    jobs_completed: u64,
    jobs_failed: u64,
    total_earned: Balance,
    registered_at: Timestamp,
    is_active: bool,
}

pub struct Bid {
    job_id: u64,
    provider: AccountId,
    price: Balance,
    sla_commitment: SLACommitment,
    submitted_at: Timestamp,
}

pub struct SLACommitment {
    max_completion_time: u64,
    guaranteed_uptime: u8, // percentage
    quality_guarantee: u8, // 0-100 score
}
```

**Reputation Algorithm**:
```
reputation_score = (
    (jobs_completed * 100) / (jobs_completed + jobs_failed) * 0.5 +
    average_quality_score * 0.3 +
    on_time_completion_rate * 0.2
) * stake_multiplier

stake_multiplier = min(1.5, 1.0 + (stake / minimum_stake) * 0.1)
```

**Key Functions**:
```rust
#[ink(message, payable)]
pub fn register_provider(
    &mut self,
    endpoint: String,
    hourly_rate: Balance,
    capabilities: Vec<ComputeType>,
) -> Result<(), Error>

#[ink(message)]
pub fn submit_bid(
    &mut self,
    job_id: u64,
    price: Balance,
    sla_commitment: SLACommitment,
) -> Result<(), Error>

#[ink(message)]
pub fn update_reputation(
    &mut self,
    provider: AccountId,
    score_delta: i32,
) -> Result<(), Error>
```

---

#### Data NFT Registry Contract

**Responsibilities**:
- Mint NFTs representing data streams
- Manage access control and subscriptions
- Track data usage and payments
- Support privacy-preserving metadata

**Data Structures**:
```rust
pub struct DataNFT {
    token_id: u64,
    owner: AccountId,
    metadata: String,
    access_price: Balance,
    privacy_level: PrivacyLevel,
    is_transferable: bool,
    created_at: Timestamp,
    total_revenue: Balance,
    access_count: u64,
}

pub enum PrivacyLevel {
    Public,      // Open access
    Private,     // Subscription required
    Confidential, // Phala TEE required
}

pub struct AccessGrant {
    token_id: u64,
    grantee: AccountId,
    granted_at: Timestamp,
    expires_at: Option<Timestamp>,
    access_count: u64,
}
```

**Access Control Flow**:
```
1. Buyer requests access
   ↓
2. Payment submitted
   ↓
3. Access granted (time-limited token)
   ↓
4. Data accessed via token
   ↓
5. Usage tracked
   ↓
6. Revenue distributed to owner
```

**Key Functions**:
```rust
#[ink(message)]
pub fn mint(
    &mut self,
    metadata: String,
    access_price: Balance,
    privacy_level: PrivacyLevel,
    is_transferable: bool,
) -> Result<u64, Error>

#[ink(message, payable)]
pub fn grant_access(
    &mut self,
    token_id: u64,
    grantee: AccountId,
    duration: Option<u64>,
) -> Result<(), Error>

#[ink(message)]
pub fn check_access(
    &self,
    token_id: u64,
    address: AccountId,
) -> bool
```

---

#### MEV Protection Contract

**Responsibilities**:
- Analyze transaction patterns
- Detect MEV attempts (sandwich, front-running)
- Calculate risk scores
- Emit alerts for suspicious activity

**Detection Algorithms**:

1. **Sandwich Attack Detection**:
```
IF transaction_before.target == transaction_after.target
   AND transaction_before.sender == transaction_after.sender
   AND user_transaction.timestamp BETWEEN (before, after)
   AND price_impact > threshold
THEN flag_as_sandwich_attack
```

2. **Front-Running Detection**:
```
IF pending_transaction.sender != mempool_transaction.sender
   AND mempool_transaction.gas_price > pending_transaction.gas_price
   AND mempool_transaction.target == pending_transaction.target
   AND mempool_transaction.timestamp < pending_transaction.timestamp
THEN flag_as_front_running
```

**Key Functions**:
```rust
#[ink(message)]
pub fn analyze_transaction(
    &mut self,
    tx_data: TransactionData,
) -> Result<MEVRiskScore, Error>

#[ink(message)]
pub fn report_mev_incident(
    &mut self,
    tx_hash: Hash,
    evidence: Vec<u8>,
) -> Result<(), Error>
```

---

#### Phala Job Processor Contract

**Responsibilities**:
- Coordinate with off-chain Phat Contract
- Verify TEE attestation proofs
- Record confidential job results
- Manage encrypted job payloads

**Integration with Phat Contract**:
```
On-Chain (PhalaJobProcessor)          Off-Chain (Phat Contract)
        │                                      │
        │  1. JobSubmitted Event               │
        │ ──────────────────────────────────►  │
        │                                      │
        │                           2. Fetch Job Data
        │                                      │
        │                           3. Execute in TEE
        │                                      │
        │  4. Submit Result + Attestation      │
        │ ◄──────────────────────────────────  │
        │                                      │
        │  5. Verify Attestation               │
        │                                      │
        │  6. Record Result                    │
        │                                      │
```

**Key Functions**:
```rust
#[ink(message)]
pub fn submit_confidential_job(
    &mut self,
    encrypted_payload: Vec<u8>,
    public_key: Vec<u8>,
) -> Result<u64, Error>

#[ink(message)]
pub fn record_attestation(
    &mut self,
    job_id: u64,
    result_hash: Hash,
    attestation_proof: Vec<u8>,
) -> Result<(), Error>
```

---

### Layer 2: SDK (Application Layer)

#### TypeScript SDK Architecture

**Directory Structure**:
```
src/
├── contracts/           # Contract wrapper classes
│   ├── BaseContract.ts  # Abstract base class
│   ├── PolkaMesh.ts     # Main SDK entry point
│   ├── AIJobQueue.ts
│   ├── PaymentEscrow.ts
│   ├── ComputeProviderRegistry.ts
│   ├── DataNFTRegistry.ts
│   ├── MEVProtection.ts
│   └── PhalaJobProcessor.ts
│
├── types/               # Type definitions
│   ├── index.ts         # Main type exports
│   ├── contracts.ts     # Contract types
│   ├── jobs.ts          # Job-related types
│   ├── providers.ts     # Provider types
│   └── nft.ts           # NFT types
│
├── utils/               # Utility functions
│   ├── address.ts       # Address validation/conversion
│   ├── balance.ts       # Balance conversion
│   ├── gas.ts           # Gas estimation
│   └── formatting.ts    # Data formatting
│
├── errors/              # Error classes
│   ├── ValidationError.ts
│   ├── ContractCallError.ts
│   ├── InsufficientBalanceError.ts
│   └── UnauthorizedError.ts
│
├── encryption/          # Encryption utilities
│   ├── ecies.ts         # ECIES encryption
│   ├── keygen.ts        # Key generation
│   ├── attestation.ts   # Attestation verification
│   └── config.ts        # Crypto configuration
│
└── index.ts             # Main export
```

#### PolkaMesh Main Class

```typescript
export class PolkaMesh {
  private api?: ApiPromise;
  private signer?: KeyringPair;
  private config: PolkaMeshConfig;

  // Contract instances
  private aiJobQueue?: AIJobQueue;
  private paymentEscrow?: PaymentEscrow;
  private computeProviderRegistry?: ComputeProviderRegistry;
  private dataNFTRegistry?: DataNFTRegistry;

  constructor(config: PolkaMeshConfig) {
    this.config = config;
  }

  async initialize(): Promise<void> {
    // Connect to Polkadot node
    const provider = new WsProvider(this.config.rpcUrl);
    this.api = await ApiPromise.create({ provider });

    // Initialize contract instances
    this.aiJobQueue = new AIJobQueue(
      this.api,
      this.config.contractAddresses.aiJobQueue
    );
    // ... initialize other contracts
  }

  // Getters for contract instances
  getAIJobQueue(): AIJobQueue {
    if (!this.aiJobQueue) throw new Error('SDK not initialized');
    return this.aiJobQueue;
  }

  // ... other getters
}
```

#### Contract Wrapper Pattern

All contract wrappers extend `BaseContract`:

```typescript
export abstract class BaseContract {
  protected api: ApiPromise;
  protected contract: ContractPromise;
  protected address: string;

  constructor(api: ApiPromise, address: string, abi: any) {
    this.api = api;
    this.address = address;
    this.contract = new ContractPromise(api, abi, address);
  }

  // Common read method
  protected async query<T>(
    method: string,
    signer: KeyringPair,
    ...args: any[]
  ): Promise<Result<T, string>> {
    const gasLimit = this.api.registry.createType('WeightV2', {
      refTime: MAX_CALL_WEIGHT,
      proofSize: PROOF_SIZE,
    });

    const { result, output } = await this.contract.query[method](
      signer.address,
      { gasLimit },
      ...args
    );

    if (result.isErr) {
      return Err(result.asErr.toString());
    }

    return Ok(output?.toJSON() as T);
  }

  // Common write method
  protected async execute(
    method: string,
    signer: KeyringPair,
    value: BN,
    ...args: any[]
  ): Promise<Result<Hash, string>> {
    const gasLimit = this.api.registry.createType('WeightV2', {
      refTime: MAX_CALL_WEIGHT,
      proofSize: PROOF_SIZE,
    });

    const tx = this.contract.tx[method](
      { gasLimit, value },
      ...args
    );

    return new Promise((resolve) => {
      tx.signAndSend(signer, ({ status, events }) => {
        if (status.isInBlock) {
          resolve(Ok(status.asInBlock));
        }
      });
    });
  }
}
```

Example contract wrapper:

```typescript
export class AIJobQueue extends BaseContract {
  async submitJob(
    params: SubmitJobParams,
    signer: KeyringPair
  ): Promise<number> {
    const result = await this.execute(
      'submitJob',
      signer,
      new BN(params.budget),
      params.description,
      params.budget,
      params.dataSetId,
      params.computeType,
      params.estimatedRuntime
    );

    if (result.isErr) {
      throw new ContractCallError(result.error);
    }

    // Parse job ID from events
    return this.parseJobIdFromEvents(result.value);
  }

  async getJob(
    jobId: number,
    signer: KeyringPair
  ): Promise<Result<Job, string>> {
    return this.query<Job>('getJob', signer, jobId);
  }
}
```

---

## Data Flow & Interactions

### Job Submission Flow (End-to-End)

```
┌──────────┐
│   User   │
└────┬─────┘
     │
     │ 1. Create job parameters
     ▼
┌────────────────┐
│   Frontend     │
│   (React UI)   │
└────┬───────────┘
     │
     │ 2. Call SDK
     ▼
┌────────────────────────────────┐
│   PolkaMesh SDK                │
│   sdk.getAIJobQueue()          │
│      .submitJob(params)        │
└────┬───────────────────────────┘
     │
     │ 3. Build transaction
     ▼
┌────────────────────────────────┐
│   Polkadot.js API              │
│   Contract.tx.submitJob()      │
└────┬───────────────────────────┘
     │
     │ 4. Sign & broadcast
     ▼
┌────────────────────────────────┐
│   Polkadot Network             │
│   (Paseo Pop)                  │
└────┬───────────────────────────┘
     │
     │ 5. Execute contract
     ▼
┌────────────────────────────────┐
│   AIJobQueue Contract          │
│   - Validate params            │
│   - Create job record          │
│   - Emit JobSubmitted event    │
└────┬───────────────────────────┘
     │
     │ 6. Trigger escrow
     ▼
┌────────────────────────────────┐
│   PaymentEscrow Contract       │
│   - Lock funds                 │
│   - Emit EscrowDeposited       │
└────┬───────────────────────────┘
     │
     │ 7. Providers discover job
     ▼
┌────────────────────────────────┐
│   Event Listener (Off-chain)   │
│   - Index JobSubmitted event   │
│   - Notify providers           │
└────┬───────────────────────────┘
     │
     │ 8. Provider bids
     ▼
┌────────────────────────────────┐
│   ComputeProviderRegistry      │
│   - Record bid                 │
│   - Emit BidSubmitted          │
└────┬───────────────────────────┘
     │
     │ 9. Select best bid
     ▼
┌────────────────────────────────┐
│   AIJobQueue Contract          │
│   - Assign provider            │
│   - Update job status          │
│   - Emit ProviderAssigned      │
└────────────────────────────────┘
```

### Provider Job Execution Flow

```
┌──────────────────┐
│   Provider Node  │
└────┬─────────────┘
     │
     │ 1. Listen for assignments
     ▼
┌────────────────────────────────┐
│   Event Listener               │
│   - Watch ProviderAssigned     │
└────┬───────────────────────────┘
     │
     │ 2. Fetch job details
     ▼
┌────────────────────────────────┐
│   SDK Query                    │
│   getJob(jobId)                │
└────┬───────────────────────────┘
     │
     │ 3. Download data (if needed)
     ▼
┌────────────────────────────────┐
│   DataNFTRegistry              │
│   - Verify access              │
│   - Return data URL            │
└────┬───────────────────────────┘
     │
     │ 4. Execute compute
     ▼
┌────────────────────────────────┐
│   Compute Engine               │
│   - GPU/CPU execution          │
│   - OR Phala TEE execution     │
└────┬───────────────────────────┘
     │
     │ 5. Upload results to IPFS
     ▼
┌────────────────────────────────┐
│   IPFS Network                 │
│   - Store result data          │
│   - Return hash                │
└────┬───────────────────────────┘
     │
     │ 6. Submit completion
     ▼
┌────────────────────────────────┐
│   AIJobQueue.markCompleted()   │
│   - Update job status          │
│   - Store result hash          │
│   - Emit JobCompleted          │
└────┬───────────────────────────┘
     │
     │ 7. Release payment
     ▼
┌────────────────────────────────┐
│   PaymentEscrow                │
│   - Release to provider        │
│   - Emit PaymentReleased       │
└────┬───────────────────────────┘
     │
     │ 8. Update reputation
     ▼
┌────────────────────────────────┐
│   ComputeProviderRegistry      │
│   - Increment jobs_completed   │
│   - Update reputation_score    │
└────────────────────────────────┘
```

---

## Security Architecture

### 1. Smart Contract Security

#### Access Control

```rust
// Example: Only job submitter can cancel
#[ink(message)]
pub fn cancel_job(&mut self, job_id: u64) -> Result<(), Error> {
    let caller = self.env().caller();
    let job = self.jobs.get(&job_id).ok_or(Error::JobNotFound)?;

    // Access control check
    if job.submitter != caller {
        return Err(Error::Unauthorized);
    }

    // ... rest of logic
}
```

#### Reentrancy Protection

```rust
// Use check-effects-interactions pattern
#[ink(message)]
pub fn release_to_provider(&mut self, job_id: u64) -> Result<(), Error> {
    // 1. CHECKS
    let escrow = self.escrows.get(&job_id).ok_or(Error::EscrowNotFound)?;
    ensure!(escrow.status == EscrowStatus::Locked, Error::InvalidStatus);

    // 2. EFFECTS (update state first)
    self.escrows.get_mut(&job_id).unwrap().status = EscrowStatus::Released;

    // 3. INTERACTIONS (external calls last)
    self.env().transfer(escrow.provider.unwrap(), escrow.amount)?;

    Ok(())
}
```

#### Input Validation

```rust
#[ink(message)]
pub fn submit_job(
    &mut self,
    description: String,
    budget: Balance,
    // ...
) -> Result<u64, Error> {
    // Validate description length
    ensure!(description.len() <= MAX_DESCRIPTION_LENGTH, Error::DescriptionTooLong);

    // Validate budget
    ensure!(budget >= self.min_job_budget, Error::BudgetTooLow);
    ensure!(budget <= MAX_JOB_BUDGET, Error::BudgetTooHigh);

    // ... rest of logic
}
```

### 2. SDK Security

#### Address Validation

```typescript
export function validateAddress(address: string): void {
  if (!isValidAddress(address)) {
    throw new ValidationError(`Invalid Polkadot address: ${address}`);
  }
}

export function isValidAddress(address: string): boolean {
  try {
    decodeAddress(address);
    return true;
  } catch {
    return false;
  }
}
```

#### Balance Validation

```typescript
export function validateBalance(balance: string | BN): void {
  const bn = typeof balance === 'string' ? new BN(balance) : balance;

  if (bn.isNeg()) {
    throw new ValidationError('Balance cannot be negative');
  }

  if (bn.gt(MAX_BALANCE)) {
    throw new ValidationError('Balance exceeds maximum');
  }
}
```

### 3. Encryption & Privacy

#### ECIES Encryption for Job Payloads

```typescript
import { encrypt, decrypt } from 'eciesjs';

export class EncryptionService {
  // Encrypt sensitive job data
  static async encryptJobPayload(
    payload: JobPayload,
    publicKey: string
  ): Promise<EncryptedPayload> {
    const payloadBytes = Buffer.from(JSON.stringify(payload));
    const publicKeyBytes = Buffer.from(publicKey, 'hex');

    const encrypted = encrypt(publicKeyBytes, payloadBytes);

    return {
      ciphertext: encrypted.toString('hex'),
      ephemeralPublicKey: publicKey,
      mac: this.computeMAC(encrypted),
    };
  }

  // Decrypt job payload (provider side)
  static async decryptJobPayload(
    encrypted: EncryptedPayload,
    privateKey: string
  ): Promise<JobPayload> {
    const privateKeyBytes = Buffer.from(privateKey, 'hex');
    const ciphertextBytes = Buffer.from(encrypted.ciphertext, 'hex');

    // Verify MAC
    if (!this.verifyMAC(encrypted)) {
      throw new Error('MAC verification failed');
    }

    const decrypted = decrypt(privateKeyBytes, ciphertextBytes);
    return JSON.parse(decrypted.toString());
  }
}
```

#### Attestation Verification (Phala TEE)

```typescript
export class AttestationService {
  static async verifyAttestation(
    attestationProof: AttestationProof,
    expectedData: Hash
  ): Promise<boolean> {
    // 1. Verify signature
    const isValidSignature = await this.verifySignature(
      attestationProof.signature,
      attestationProof.publicKey
    );

    if (!isValidSignature) {
      return false;
    }

    // 2. Verify data hash matches
    const computedHash = blake2b256(attestationProof.data);
    if (computedHash !== expectedData) {
      return false;
    }

    // 3. Verify TEE quote (SGX/TDX)
    const isValidQuote = await this.verifyTEEQuote(
      attestationProof.quote
    );

    return isValidQuote;
  }
}
```

---

## Scalability & Performance

### 1. Gas Optimization

#### Batch Operations

```typescript
// Instead of submitting jobs one by one
for (const jobParams of jobs) {
  await sdk.getAIJobQueue().submitJob(jobParams);
  // 6 transactions = 6 * gas cost
}

// Batch submit (future feature)
await sdk.getAIJobQueue().submitJobBatch(jobs);
// 1 transaction = 1 * gas cost (with slight overhead)
```

#### Storage Optimization

```rust
// Store only essential data on-chain
pub struct Job {
    id: u64,
    submitter: AccountId,
    // ... essential fields only

    // AVOID: Large data on-chain
    // dataset: Vec<u8>,  // ❌ Don't store large data

    // INSTEAD: Store hash/reference
    data_ref: Hash,  // ✅ Store IPFS hash
}
```

### 2. Indexing & Querying

#### Event Indexing with SubQuery

```graphql
# Example SubQuery schema
type Job @entity {
  id: ID!
  jobId: BigInt!
  submitter: String!
  provider: String
  budget: BigInt!
  status: JobStatus!
  submittedAt: BigInt!
  completedAt: BigInt
  resultHash: String
}

type Provider @entity {
  id: ID!
  address: String!
  reputationScore: Int!
  jobsCompleted: BigInt!
  totalEarned: BigInt!
}
```

Query example:
```graphql
query {
  jobs(
    filter: { status: { equalTo: COMPLETED } }
    orderBy: SUBMITTED_AT_DESC
    first: 10
  ) {
    nodes {
      jobId
      submitter
      provider
      budget
      completedAt
    }
  }
}
```

### 3. Caching Strategy

```typescript
export class CachedPolkaMesh extends PolkaMesh {
  private cache: Map<string, CacheEntry> = new Map();
  private cacheTTL = 60000; // 1 minute

  async getJob(jobId: number): Promise<Job> {
    const cacheKey = `job:${jobId}`;
    const cached = this.cache.get(cacheKey);

    if (cached && Date.now() - cached.timestamp < this.cacheTTL) {
      return cached.data;
    }

    const job = await super.getJob(jobId);
    this.cache.set(cacheKey, {
      data: job,
      timestamp: Date.now(),
    });

    return job;
  }
}
```

---

## Integration Patterns

### 1. Frontend Integration

#### React Hook Example

```typescript
// usePolkaMesh.ts
export function usePolkaMesh() {
  const [sdk, setSdk] = useState<PolkaMesh | null>(null);
  const [isInitialized, setIsInitialized] = useState(false);

  useEffect(() => {
    const initSDK = async () => {
      const polkaMesh = new PolkaMesh({
        rpcUrl: 'wss://rpc1.paseo.popnetwork.xyz',
        contractAddresses: { /* ... */ },
      });

      await polkaMesh.initialize();
      setSdk(polkaMesh);
      setIsInitialized(true);
    };

    initSDK();
  }, []);

  return { sdk, isInitialized };
}

// Component usage
function JobSubmissionForm() {
  const { sdk, isInitialized } = usePolkaMesh();
  const { signer } = useWallet();

  const handleSubmit = async (params: JobParams) => {
    if (!sdk || !signer) return;

    const jobId = await sdk.getAIJobQueue().submitJob(params, signer);
    console.log('Job submitted:', jobId);
  };

  // ... render form
}
```

### 2. Backend Integration

#### Node.js Service

```typescript
// job-service.ts
export class JobService {
  private sdk: PolkaMesh;

  async initialize() {
    this.sdk = new PolkaMesh(config);
    await this.sdk.initialize();
  }

  async submitJob(
    userId: string,
    params: JobParams
  ): Promise<number> {
    // Get user's keypair from secure storage
    const signer = await this.getUserSigner(userId);

    // Submit job
    const jobId = await this.sdk
      .getAIJobQueue()
      .submitJob(params, signer);

    // Store in database for tracking
    await db.jobs.create({
      jobId,
      userId,
      status: 'submitted',
      createdAt: new Date(),
    });

    return jobId;
  }

  async monitorJobStatus(jobId: number): Promise<void> {
    const job = await this.sdk
      .getAIJobQueue()
      .getJob(jobId, this.adminSigner);

    // Update database
    await db.jobs.update(jobId, {
      status: job.status,
      updatedAt: new Date(),
    });

    // Notify user if completed
    if (job.status === 'Completed') {
      await this.notifyUser(job.submitter, jobId);
    }
  }
}
```

### 3. Provider Integration

#### Compute Provider Worker

```typescript
// provider-worker.ts
export class ProviderWorker {
  private sdk: PolkaMesh;
  private providerKey: KeyringPair;

  async start() {
    // Initialize SDK
    this.sdk = new PolkaMesh(config);
    await this.sdk.initialize();

    // Load provider credentials
    this.providerKey = await this.loadProviderKey();

    // Start listening for jobs
    await this.listenForJobs();
  }

  private async listenForJobs() {
    // Subscribe to JobSubmitted events
    const api = await this.sdk.getApi();

    api.query.system.events((events) => {
      events.forEach((record) => {
        const { event } = record;

        if (event.section === 'contracts' &&
            event.method === 'ContractEmitted') {
          const decoded = this.decodeEvent(event);

          if (decoded.eventName === 'JobSubmitted') {
            this.handleNewJob(decoded.jobId);
          }
        }
      });
    });
  }

  private async handleNewJob(jobId: number) {
    // Get job details
    const job = await this.sdk
      .getAIJobQueue()
      .getJob(jobId, this.providerKey);

    // Check if we can handle this job
    if (!this.canHandleJob(job)) {
      return;
    }

    // Submit bid
    await this.sdk
      .getComputeProviderRegistry()
      .submitBid({
        jobId,
        price: this.calculatePrice(job),
        slaCommitment: this.getSLACommitment(),
      }, this.providerKey);
  }

  async executeJob(jobId: number) {
    // Mark as in progress
    await this.sdk
      .getAIJobQueue()
      .markInProgress(jobId, this.providerKey);

    // Execute compute task
    const result = await this.compute(jobId);

    // Upload result to IPFS
    const resultHash = await this.uploadToIPFS(result);

    // Mark as completed
    await this.sdk
      .getAIJobQueue()
      .markCompleted(jobId, resultHash, this.providerKey);
  }
}
```

---

## Deployment Architecture

### Testnet Deployment (Current)

```
┌─────────────────────────────────────────────────┐
│           Paseo Pop Network (Testnet)           │
│                                                  │
│  ┌────────────────────────────────────────┐    │
│  │  Deployed Smart Contracts              │    │
│  │                                         │    │
│  │  • AIJobQueue                           │    │
│  │    0xa44639cd0d0e6c6607491088c9c549... │    │
│  │                                         │    │
│  │  • PaymentEscrow                        │    │
│  │    0x5a86a13ef7fc1c5e58f022be183de0... │    │
│  │                                         │    │
│  │  • ComputeProviderRegistry              │    │
│  │    0x2c6fc00458f198f46ef072e1516b83... │    │
│  │                                         │    │
│  │  • DataNFTRegistry                      │    │
│  │    0x6dc84ddeffccb19ed5285cf3c3d7b0... │    │
│  │                                         │    │
│  │  • PhalaJobProcessor (SS58)             │    │
│  │    5HrKZAiTSAFcuxda89kSD77ZdygRUku... │    │
│  │                                         │    │
│  │  • MEVProtection (SS58)                 │    │
│  │    5DTPZHSHydkPQZbTFrhnHtZiDER7uoK... │    │
│  └────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

### Production Deployment (Future)

```
┌─────────────────────────────────────────────────────────┐
│                  Polkadot Mainnet                        │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │            Asset Hub Parachain                    │  │
│  │  (Primary contract deployment)                    │  │
│  │                                                    │  │
│  │  • AIJobQueue                                     │  │
│  │  • PaymentEscrow (with XCM support)               │  │
│  │  • ComputeProviderRegistry                        │  │
│  │  • DataNFTRegistry                                │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │            Phala Network Parachain                │  │
│  │  (Confidential compute)                           │  │
│  │                                                    │  │
│  │  • PhalaJobProcessor (on-chain)                   │  │
│  │  • Phat Contract (off-chain worker)               │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │            Acurast Parachain                      │  │
│  │  (Decentralized cloud compute)                    │  │
│  │                                                    │  │
│  │  • Provider integration                           │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│              ┌────────────────┐                         │
│              │  XCM Channels  │                         │
│              │                │                         │
│              │  Asset Hub ◄───► Phala                  │
│              │  Asset Hub ◄───► Acurast                │
│              └────────────────┘                         │
└─────────────────────────────────────────────────────────┘

                         │
                         │ RPC / WebSocket
                         │
                         ▼

┌─────────────────────────────────────────────────────────┐
│               Infrastructure Layer                       │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   Frontend   │  │   Backend    │  │    IPFS      │  │
│  │   (Vercel)   │  │  (AWS/GCP)   │  │   (Pinata)   │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   SubQuery   │  │  Monitoring  │  │   Analytics  │  │
│  │  (Indexing)  │  │(Prometheus)  │  │  (Grafana)   │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└─────────────────────────────────────────────────────────┘
```

---

## Conclusion

PolkaMesh's architecture is designed for:

- ✅ **Modularity**: Independent, upgradeable components
- ✅ **Scalability**: Efficient gas usage, caching, indexing
- ✅ **Security**: Multi-layered protection, encryption, access control
- ✅ **Privacy**: TEE integration, encrypted payloads, ZK proofs
- ✅ **Interoperability**: Cross-chain payments via XCM
- ✅ **Developer Experience**: Type-safe SDK, comprehensive docs

This architecture provides a solid foundation for a production-ready decentralized AI compute and data marketplace on Polkadot.

---

**Document Version**: 1.0
**Last Updated**: November 17, 2025
**For**: Polkadot Cloud Hackathon Submission
