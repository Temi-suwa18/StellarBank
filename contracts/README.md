# 🔗 StellarBank Smart Contracts

> Secure Soroban smart contracts powering cross-border remittance operations on Stellar

[![Soroban](https://img.shields.io/badge/Soroban-20.1.0-blue)](https://soroban.stellar.org)
[![Rust](https://img.shields.io/badge/Rust-1.72%2B-orange)](https://rustlang.org)
[![Stellar](https://img.shields.io/badge/Stellar-Network-brightgreen)](https://stellar.org)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🌟 Contract Suite Overview

StellarBank's smart contract architecture provides secure, efficient, and compliant cross-border payment processing through a modular design:

### 🏦 **Remittance Pool Contract**
- Liquidity management for cross-border transfers
- Automated market making for currency pairs
- Dynamic fee calculation based on liquidity
- Slippage protection and rate guarantees

### 🔄 **Exchange Handler Contract** 
- Real-time currency conversion via Stellar DEX
- Multi-hop routing for optimal rates
- Atomic swap execution
- Price oracle integration

### 🛡️ **Compliance Oracle Contract**
- KYC/AML compliance checking
- Transaction limit enforcement
- Sanctions list validation
- Regulatory reporting automation

### 🔐 **Multi-Signature Escrow Contract**
- Secure fund holding during processing
- Dispute resolution mechanisms
- Automated release conditions
- Emergency pause functionality

---

## 🚀 Quick Start

### Prerequisites
```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.cargo/env

# Install Soroban CLI
cargo install --locked soroban-cli

# Add Stellar target
rustup target add wasm32-unknown-unknown
```

### Build & Deploy
```bash
# Clone repository
git clone https://github.com/presidojay1/StellarBank.git
cd StellarBank/contracts

# Build all contracts
cargo build --target wasm32-unknown-unknown --release

# Deploy to testnet
soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/remittance_pool.wasm \
  --source-account ADMIN_SECRET_KEY \
  --network testnet

# Initialize contracts
soroban contract invoke \
  --id CONTRACT_ID \
  --source-account ADMIN_SECRET_KEY \
  --network testnet \
  -- initialize \
  --admin ADMIN_PUBLIC_KEY \
  --token_a USDC_CONTRACT_ID \
  --token_b NGNT_CONTRACT_ID
```

### Testing
```bash
# Run unit tests
cargo test

# Run integration tests with Soroban CLI
cargo test --test integration_tests

# Test coverage
cargo tarpaulin --out Html
```

---

## 🏗️ Architecture

```
StellarBank Smart Contracts
├── 🏦 remittance-pool/
│   ├── src/
│   │   ├── lib.rs              # Main contract logic
│   │   ├── storage.rs          # Data structures
│   │   ├── liquidity.rs        # Liquidity management
│   │   ├── pricing.rs          # Price calculation
│   │   └── error.rs            # Error definitions
│   ├── tests/
│   └── Cargo.toml
├── 🔄 exchange-handler/
│   ├── src/
│   │   ├── lib.rs              # Exchange logic
│   │   ├── router.rs           # Path finding
│   │   ├── dex.rs              # DEX integration
│   │   └── oracle.rs           # Price oracle
│   ├── tests/
│   └── Cargo.toml
├── 🛡️ compliance-oracle/
│   ├── src/
│   │   ├── lib.rs              # Compliance engine
│   │   ├── kyc.rs              # KYC validation
│   │   ├── limits.rs           # Transaction limits
│   │   └── sanctions.rs        # Sanctions screening
│   ├── tests/
│   └── Cargo.toml
└── 🔐 multi-sig-escrow/
    ├── src/
    │   ├── lib.rs              # Escrow logic
    │   ├── multisig.rs         # Multi-signature
    │   ├── timelock.rs         # Time-based locks
    │   └── dispute.rs          # Dispute resolution
    ├── tests/
    └── Cargo.toml
```

---

## 🏦 Remittance Pool Contract

### **Core Functionality**
- **Liquidity Provision:** Users can provide liquidity for currency pairs
- **Automated Market Making:** Dynamic pricing based on constant product formula
- **Cross-Border Transfers:** Instant settlement with guaranteed rates
- **Fee Distribution:** Proportional rewards for liquidity providers

### **Key Features**
```rust
// Main contract interface
#[contract]
pub struct RemittancePool;

#[contractimpl]
impl RemittancePool {
    /// Initialize the remittance pool
    pub fn initialize(
        env: Env,
        admin: Address,
        token_a: Address,
        token_b: Address,
        fee_rate: u32,
    ) -> Result<(), PoolError>;

    /// Add liquidity to the pool
    pub fn add_liquidity(
        env: Env,
        user: Address,
        amount_a: i128,
        amount_b: i128,
    ) -> Result<i128, PoolError>;

    /// Execute cross-border transfer
    pub fn transfer_cross_border(
        env: Env,
        sender: Address,
        recipient: Address,
        amount: i128,
        destination_currency: Address,
    ) -> Result<TransferResult, PoolError>;

    /// Get current exchange rate
    pub fn get_exchange_rate(
        env: Env,
        from_token: Address,
        to_token: Address,
    ) -> Result<Rate, PoolError>;
}
```

### **Liquidity Management**
```rust
// Liquidity provider functions
pub fn calculate_lp_tokens(
    reserve_a: i128,
    reserve_b: i128,
    amount_a: i128,
    amount_b: i128,
) -> i128 {
    // Constant product formula: x * y = k
    let total_supply = get_total_lp_supply();
    if total_supply == 0 {
        return (amount_a * amount_b).sqrt();
    }
    
    min(
        (amount_a * total_supply) / reserve_a,
        (amount_b * total_supply) / reserve_b,
    )
}
```

---

## 🔄 Exchange Handler Contract

### **Real-Time Currency Conversion**
- **DEX Integration:** Direct integration with Stellar DEX
- **Multi-Hop Routing:** Find optimal conversion paths
- **Slippage Protection:** Maximum slippage tolerance
- **Rate Locking:** Guaranteed rates for time periods

### **Smart Routing Algorithm**
```rust
/// Find the best exchange path for currency conversion
pub fn find_best_path(
    env: &Env,
    from_asset: Address,
    to_asset: Address,
    amount: i128,
) -> Result<ExchangePath, ExchangeError> {
    let paths = discover_paths(&env, &from_asset, &to_asset);
    let mut best_path = None;
    let mut best_output = 0i128;

    for path in paths {
        let output = simulate_path_execution(&env, &path, amount)?;
        if output > best_output {
            best_output = output;
            best_path = Some(path);
        }
    }

    best_path.ok_or(ExchangeError::NoPathFound)
}
```

### **Atomic Swaps**
```rust
/// Execute atomic currency swap
pub fn execute_swap(
    env: Env,
    trader: Address,
    path: ExchangePath,
    amount_in: i128,
    min_amount_out: i128,
) -> Result<SwapResult, ExchangeError> {
    // Validate inputs
    require_authorized(&env, &trader);
    
    // Execute swap atomically
    let amount_out = execute_path(&env, &path, amount_in)?;
    
    if amount_out < min_amount_out {
        return Err(ExchangeError::InsufficientOutput);
    }

    Ok(SwapResult {
        amount_in,
        amount_out,
        path,
        fees_paid: calculate_fees(&path, amount_in),
    })
}
```

---

## 🛡️ Compliance Oracle Contract

### **Regulatory Compliance Engine**
- **KYC Verification:** Identity verification status checking
- **AML Screening:** Anti-money laundering compliance
- **Transaction Limits:** Daily/monthly limits enforcement
- **Sanctions Checking:** Real-time sanctions list validation

### **KYC Integration**
```rust
/// Verify KYC status for cross-border transfer
pub fn verify_kyc_status(
    env: Env,
    user: Address,
    amount: i128,
    destination_country: String,
) -> Result<ComplianceResult, ComplianceError> {
    let kyc_level = get_user_kyc_level(&env, &user)?;
    let limits = get_country_limits(&env, &destination_country)?;
    
    if amount > limits.max_amount_for_level(kyc_level) {
        return Err(ComplianceError::ExceedsLimit);
    }

    // Check sanctions lists
    if is_sanctioned(&env, &user)? {
        return Err(ComplianceError::SanctionedEntity);
    }

    Ok(ComplianceResult::Approved)
}
```

### **Dynamic Limits**
```rust
/// Calculate transaction limits based on user profile
pub fn calculate_limits(
    env: &Env,
    user: &Address,
    destination: &str,
) -> Result<TransactionLimits, ComplianceError> {
    let kyc_level = get_user_kyc_level(env, user)?;
    let country_rules = get_country_regulations(env, destination)?;
    let user_history = get_transaction_history(env, user, 30)?; // Last 30 days

    TransactionLimits {
        daily_limit: min(
            kyc_level.daily_limit(),
            country_rules.incoming_limit,
        ),
        monthly_limit: min(
            kyc_level.monthly_limit(),
            country_rules.monthly_limit,
        ),
        remaining_daily: calculate_remaining_daily(user_history),
        remaining_monthly: calculate_remaining_monthly(user_history),
    }
}
```

---

## 🔐 Multi-Signature Escrow Contract

### **Secure Fund Management**
- **Multi-Signature Security:** Configurable signature requirements
- **Time-Based Locks:** Automatic release after time periods
- **Dispute Resolution:** Mediator-based conflict resolution
- **Emergency Procedures:** Admin override for critical situations

### **Escrow Creation**
```rust
/// Create new escrow for cross-border transfer
pub fn create_escrow(
    env: Env,
    sender: Address,
    recipient: Address,
    mediator: Address,
    amount: i128,
    token: Address,
    release_conditions: ReleaseConditions,
) -> Result<Address, EscrowError> {
    require_authorized(&env, &sender);
    
    let escrow_id = generate_escrow_id(&env);
    
    // Transfer funds to escrow
    transfer_to_escrow(&env, &sender, &token, amount)?;
    
    // Set up escrow parameters
    let escrow = EscrowData {
        sender: sender.clone(),
        recipient: recipient.clone(),
        mediator: mediator.clone(),
        amount,
        token: token.clone(),
        status: EscrowStatus::Pending,
        created_at: env.ledger().timestamp(),
        release_conditions,
        required_signatures: 2,
    };
    
    set_escrow_data(&env, &escrow_id, &escrow);
    
    Ok(escrow_id)
}
```

### **Signature-Based Release**
```rust
/// Release funds with multi-signature approval
pub fn release_funds(
    env: Env,
    escrow_id: Address,
    signatures: Vec<Signature>,
) -> Result<(), EscrowError> {
    let mut escrow = get_escrow_data(&env, &escrow_id)?;
    
    // Validate signatures
    let valid_sigs = validate_signatures(&env, &escrow, &signatures)?;
    
    if valid_sigs < escrow.required_signatures {
        return Err(EscrowError::InsufficientSignatures);
    }
    
    // Check release conditions
    if !check_release_conditions(&env, &escrow)? {
        return Err(EscrowError::ConditionsNotMet);
    }
    
    // Release funds to recipient
    transfer_from_escrow(&env, &escrow.recipient, &escrow.token, escrow.amount)?;
    
    escrow.status = EscrowStatus::Released;
    set_escrow_data(&env, &escrow_id, &escrow);
    
    Ok(())
}
```

---

## 🧪 Testing Suite

### **Unit Tests**
```rust
#[cfg(test)]
mod tests {
    use super::*;
    use soroban_sdk::testutils::{Address as _, Ledger, LedgerInfo};

    #[test]
    fn test_remittance_pool_initialization() {
        let env = Env::default();
        env.mock_all_auths();
        
        let admin = Address::generate(&env);
        let token_a = deploy_token_contract(&env, &admin);
        let token_b = deploy_token_contract(&env, &admin);
        
        let pool = RemittancePoolClient::new(&env, &env.register_contract(None, RemittancePool));
        
        pool.initialize(&admin, &token_a, &token_b, 30); // 0.3% fee
        
        assert_eq!(pool.get_admin(), admin);
        assert_eq!(pool.get_fee_rate(), 30);
    }

    #[test]
    fn test_cross_border_transfer() {
        let env = setup_test_environment();
        let (sender, recipient, pool) = setup_transfer_scenario(&env);
        
        let result = pool.transfer_cross_border(
            &sender,
            &recipient,
            1_000_0000000i128, // 1000 USD
            &get_ngn_token(&env),
        );
        
        assert!(result.is_ok());
        assert!(result.unwrap().amount_out > 0);
    }
}
```

### **Integration Tests**
```bash
# Test end-to-end transfer flow
cargo test test_full_remittance_flow -- --nocapture

# Test compliance integration
cargo test test_compliance_screening -- --nocapture

# Test multi-signature escrow
cargo test test_multisig_escrow_flow -- --nocapture
```

---

## 📊 Performance & Gas Optimization

### **Gas Usage Benchmarks**
| Operation | Gas Cost | Optimization |
|-----------|----------|-------------|
| Pool Initialization | ~2,000 | One-time setup |
| Add Liquidity | ~1,500 | Bulk operations |
| Cross-Border Transfer | ~3,000 | Path optimization |
| Compliance Check | ~800 | Cached results |
| Escrow Creation | ~1,200 | Minimal storage |

### **Optimization Techniques**
```rust
// Storage optimization
#[derive(Clone, Debug, Eq, PartialEq)]
pub struct OptimizedPoolData {
    // Use packed structs to minimize storage
    pub reserves: (i128, i128),           // 32 bytes
    pub fee_rate: u32,                    // 4 bytes  
    pub total_supply: i128,               // 16 bytes
    pub last_update: u64,                 // 8 bytes
}

// Batch operations for efficiency
pub fn batch_add_liquidity(
    env: Env,
    operations: Vec<LiquidityOperation>,
) -> Result<Vec<i128>, PoolError> {
    let mut results = Vec::new();
    
    for op in operations {
        let lp_tokens = add_liquidity_internal(&env, &op)?;
        results.push(lp_tokens);
    }
    
    emit_batch_events(&env, &results);
    Ok(results)
}
```

---

## 🔐 Security Considerations

### **Access Control**
```rust
/// Administrative functions require admin authorization
fn require_admin(env: &Env) -> Result<(), PoolError> {
    let admin = get_admin(env);
    env.current_contract_address().require_auth_for_args(
        &admin, 
        InvokerContractAuthEntry::Contract
    );
    Ok(())
}

/// Multi-signature validation for sensitive operations
fn require_multisig(
    env: &Env, 
    required: u32, 
    signatures: &Vec<Signature>
) -> Result<(), EscrowError> {
    if signatures.len() < required as usize {
        return Err(EscrowError::InsufficientSignatures);
    }
    
    // Validate each signature
    for sig in signatures {
        validate_signature(env, sig)?;
    }
    
    Ok(())
}
```

### **Reentrancy Protection**
```rust
// State machine pattern for reentrancy protection
#[derive(Clone, Copy, Debug, Eq, PartialEq)]
pub enum ContractState {
    Idle,
    Processing,
    Paused,
}

pub fn transfer_with_reentrancy_guard(
    env: Env,
    amount: i128,
) -> Result<(), PoolError> {
    let state = get_contract_state(&env);
    if state != ContractState::Idle {
        return Err(PoolError::ReentrantCall);
    }
    
    set_contract_state(&env, ContractState::Processing);
    
    let result = execute_transfer(&env, amount);
    
    set_contract_state(&env, ContractState::Idle);
    
    result
}
```

---

## 🚀 Deployment Guide

### **Testnet Deployment**
```bash
#!/bin/bash
# deploy-testnet.sh

# Build contracts
cargo build --target wasm32-unknown-unknown --release

# Deploy Remittance Pool
POOL_ID=$(soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/remittance_pool.wasm \
  --source-account $ADMIN_SECRET \
  --network testnet)

echo "Remittance Pool deployed: $POOL_ID"

# Initialize Pool
soroban contract invoke \
  --id $POOL_ID \
  --source-account $ADMIN_SECRET \
  --network testnet \
  -- initialize \
  --admin $ADMIN_PUBLIC \
  --token_a $USDC_CONTRACT \
  --token_b $NGNT_CONTRACT \
  --fee_rate 30

echo "Pool initialized successfully"
```

### **Mainnet Deployment Checklist**
- [ ] All tests passing (unit, integration, E2E)
- [ ] Security audit completed  
- [ ] Gas optimization verified
- [ ] Admin keys secured in hardware wallets
- [ ] Emergency pause mechanisms tested
- [ ] Compliance rules configured
- [ ] Monitoring and alerting set up
- [ ] Disaster recovery plan prepared

---

## 🤝 Contributing

### **Development Setup**
```bash
# Install development tools
rustup component add clippy rustfmt
cargo install cargo-tarpaulin

# Set up pre-commit hooks
git config core.hooksPath .githooks
chmod +x .githooks/*
```

### **Code Standards**
- Follow Rust best practices and idioms
- Use `clippy` for additional linting
- Maintain >90% test coverage
- Document all public functions
- Security-first development approach

---

## 📄 License

MIT License - see [LICENSE](../LICENSE) file for details.

---

<div align="center">

**Built with 🦀 Rust and ⭐ Stellar by the StellarBank Smart Contract Team**

*Secure, efficient, and compliant blockchain infrastructure*

</div>