# AfRamp Multi-Chain Smart Contracts

A decentralized, multi-chain on/off-ramp solution enabling seamless cryptocurrency-to-fiat conversions across African markets. Built for Stellar, EVM-compatible chains, and other blockchain networks.

## Overview

AfRamp provides a trustless escrow system connecting users who want to buy or sell cryptocurrency using local African payment methods. The platform operates across multiple blockchain networks, offering users flexibility in choosing their preferred chain while maintaining consistent functionality and security.

## Supported Chains

- **Stellar** - Primary focus for fast, low-cost transactions
- **Ethereum** - Mainnet and testnets (Sepolia, Goerli)
- **Polygon** - Low-fee EVM alternative
- **Base** - Coinbase L2 for seamless fiat integration
- **Arbitrum** - Ethereum L2 for reduced costs
- **Optimism** - Ethereum L2 alternative
- **Celo** - Mobile-first blockchain popular in Africa

## Repository Structure

```
aframp-contracts/
├── stellar/
│   ├── contracts/
│   │   ├── escrow.rs
│   │   ├── orderbook.rs
│   │   └── reputation.rs
│   ├── tests/
│   └── README.md
├── evm/
│   ├── contracts/
│   │   ├── AfRampEscrow.sol
│   │   ├── AfRampOrderBook.sol
│   │   ├── AfRampReputation.sol
│   │   └── AfRampGovernance.sol
│   ├── scripts/
│   ├── test/
│   └── README.md
├── shared/
│   ├── types/
│   ├── interfaces/
│   └── utils/
├── docs/
│   ├── architecture.md
│   ├── integration-guide.md
│   └── security.md
└── deployment/
    ├── addresses.json
    └── configs/
```

## Features

### Core Functionality
- **Multi-Chain Escrow**: Secure fund holding across all supported networks
- **Unified Order Book**: Cross-chain order matching and discovery
- **Local Payment Methods**: Support for M-Pesa, bank transfers, mobile money
- **Reputation System**: Cross-chain user ratings and transaction history
- **Dispute Resolution**: Decentralized arbitration mechanism
- **Multi-Currency Support**: NGN, KES, ZAR, GHS, UGX, TZS, and more

### Chain-Specific Features

#### Stellar
- Lightning-fast settlement (3-5 seconds)
- Minimal transaction fees (~$0.00001)
- Built-in DEX integration
- Custom asset support
- Path payments for currency conversion

#### EVM Chains
- Full smart contract composability
- DeFi protocol integration
- Token standard compatibility (ERC-20, ERC-721)
- Layer 2 scaling solutions
- Cross-chain bridges

## Getting Started

### Prerequisites

**For Stellar:**
- Rust 1.70+
- Soroban CLI
- Node.js 18+

**For EVM Chains:**
- Node.js 18+
- Hardhat or Foundry
- Solidity 0.8.20+

### Installation

```bash
# Clone the repository
git clone https://github.com/aframp/aframp-contracts.git
cd aframp-contracts

# Install dependencies
npm install

# Install Stellar/Soroban tooling
npm run install:stellar

# Install EVM tooling
npm run install:evm
```

### Stellar Contract Development

```bash
# Navigate to Stellar contracts
cd stellar

# Build contracts
cargo build --target wasm32-unknown-unknown --release

# Run tests
cargo test

# Deploy to testnet
soroban contract deploy \
  --wasm target/wasm32-unknown-unknown/release/aframp_escrow.wasm \
  --source <YOUR_SECRET_KEY> \
  --rpc-url https://soroban-testnet.stellar.org \
  --network-passphrase "Test SDF Network ; September 2015"
```

### EVM Contract Development

```bash
# Navigate to EVM contracts
cd evm

# Compile contracts
npx hardhat compile

# Run tests
npx hardhat test

# Deploy to network
npx hardhat run scripts/deploy.js --network polygon
```

## Contract Interfaces

### Stellar (Soroban)

```rust
// Create an escrow order
pub fn create_order(
    env: Env,
    seller: Address,
    amount: i128,
    asset: Address,
    fiat_currency: Symbol,
    rate: i128,
) -> u64

// Accept and lock funds
pub fn accept_order(
    env: Env,
    order_id: u64,
    buyer: Address,
) -> Result<(), Error>

// Confirm fiat payment received
pub fn confirm_payment(
    env: Env,
    order_id: u64,
) -> Result<(), Error>

// Release crypto to buyer
pub fn release_funds(
    env: Env,
    order_id: u64,
) -> Result<(), Error>
```

### EVM (Solidity)

```solidity
// Create an order
function createOrder(
    uint256 amount,
    address token,
    string memory fiatCurrency,
    uint256 rate,
    bool isBuyOrder
) external returns (uint256 orderId);

// Take an order
function takeOrder(uint256 orderId) external payable;

// Mark payment as sent
function confirmPayment(uint256 orderId) external;

// Release escrowed funds
function releaseFunds(uint256 orderId) external;

// Raise a dispute
function raiseDispute(
    uint256 orderId,
    string memory evidence
) external;
```

## Cross-Chain Architecture

AfRamp uses a hub-and-spoke model where each chain operates independently but shares order book data through:

1. **Decentralized Indexers**: Aggregate orders across all chains
2. **Oracle Network**: Provide real-time fiat exchange rates
3. **Cross-Chain Messaging**: LayerZero/Wormhole for chain communication
4. **Unified Frontend**: Single interface for all chains

## Supported Fiat Currencies & Payment Methods

| Currency | Code | Payment Methods |
|----------|------|-----------------|
| Nigerian Naira | NGN | Bank Transfer, Mobile Money |
| Kenyan Shilling | KES | M-Pesa, Airtel Money, Bank Transfer |
| South African Rand | ZAR | EFT, Capitec Pay, Instant EFT |
| Ghanaian Cedi | GHS | Mobile Money, Bank Transfer |
| Ugandan Shilling | UGX | MTN Mobile Money, Airtel Money |
| Tanzanian Shilling | TZS | M-Pesa, Tigo Pesa, Airtel Money |

## Security

### Audits
- [ ] Stellar contracts - Pending audit by [Auditor Name]
- [ ] EVM contracts - Pending audit by [Auditor Name]
- [ ] Cross-chain bridge - Pending audit

### Security Features
- Multi-signature governance for critical operations
- Time-locked escrow with automatic refunds
- Rate limiting and circuit breakers
- Reentrancy guards (EVM)
- Integer overflow protection
- Emergency pause functionality

### Bug Bounty
Active bug bounty program with rewards up to $50,000. See [SECURITY.md](./SECURITY.md) for details.

## Testing

```bash
# Run all tests across all chains
npm run test:all

# Test Stellar contracts only
npm run test:stellar

# Test EVM contracts only
npm run test:evm

# Run with coverage
npm run coverage

# Run integration tests
npm run test:integration
```

## Deployment

### Testnet Deployments

**Stellar Testnet:**
- Escrow Contract: `CCXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`
- OrderBook Contract: `CCXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`

**Polygon Mumbai:**
- AfRampEscrow: `0x...`
- AfRampOrderBook: `0x...`

**Base Sepolia:**
- AfRampEscrow: `0x...`
- AfRampOrderBook: `0x...`

See [deployment/addresses.json](./deployment/addresses.json) for complete list.

### Mainnet Deployments
Coming soon - awaiting audit completion.

## Gas/Fee Optimization

| Chain | Avg. Create Order | Avg. Complete Trade | Total Cost |
|-------|-------------------|---------------------|------------|
| Stellar | ~$0.00001 | ~$0.00002 | ~$0.00003 |
| Polygon | ~$0.01 | ~$0.02 | ~$0.03 |
| Base | ~$0.02 | ~$0.03 | ~$0.05 |
| Arbitrum | ~$0.03 | ~$0.04 | ~$0.07 |

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines.

### Development Workflow
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Community & Support

- **Documentation**: [docs.aframp.io](#)
- **Discord**: [discord.gg/aframp](#)
- **Twitter**: [@AfRampHQ](#)
- **Telegram**: [t.me/aframp](#)
- **Email**: dev@aframp.io

## Roadmap

- [x] Stellar Soroban contract development
- [x] EVM contract development (Polygon, Base, Arbitrum)
- [ ] External security audits
- [ ] Mainnet deployment
- [ ] Mobile SDK release
- [ ] Additional chain support (Solana, NEAR, Cosmos)
- [ ] Fiat on-ramp partnerships
- [ ] DAO governance implementation

## License

This project is licensed under the MIT License - see [LICENSE](./LICENSE) file for details.

## Disclaimer

AfRamp smart contracts handle financial transactions. Use at your own risk. Always conduct thorough testing and security reviews before mainnet deployment. The developers assume no liability for funds lost due to bugs, exploits, or misuse of the contracts.

---

**Built with ❤️ for Africa**
