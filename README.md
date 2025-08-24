# SatoshiSwap Protocol

[![Clarity](https://img.shields.io/badge/Clarity-Smart%20Contract-purple)](https://clarity-lang.org/)
[![Stacks](https://img.shields.io/badge/Stacks-Bitcoin%20L2-orange)](https://stacks.co/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## Overview

SatoshiSwap is an advanced, high-performance decentralized exchange protocol built natively for the Stacks blockchain, bringing institutional-grade trading infrastructure to Bitcoin's Layer 2 ecosystem with zero-trust mechanics. Engineered for maximum capital efficiency and Bitcoin alignment, SatoshiSwap delivers enterprise-grade automated market making with advanced price discovery, dynamic liquidity optimization, and MEV-resistant swap execution.

## Key Features

- 🔐 **Zero-trust Automated Market Making** with mathematical precision
- 🛡️ **Advanced Slippage Protection** and price impact controls
- ⚡ **Dynamic Fee Optimization** for maximum liquidity provider returns
- 🚫 **MEV-resistant Execution Engine** with front-running protection
- 🏛️ **Institutional-grade Pool Management** and governance
- 📊 **Real-time Price Impact Analysis** for informed trading decisions
- 🎯 **Sophisticated Trading Controls** with customizable parameters

## System Architecture

### Core Components

```
┌─────────────────────┐    ┌─────────────────────┐    ┌─────────────────────┐
│   Token Interface   │    │   Liquidity Pools   │    │   Trading Engine    │
│    (SIP-010)        │◄──►│   Management        │◄──►│   & AMM Logic       │
└─────────────────────┘    └─────────────────────┘    └─────────────────────┘
           │                           │                           │
           │                           │                           │
           ▼                           ▼                           ▼
┌─────────────────────┐    ┌─────────────────────┐    ┌─────────────────────┐
│  Position Tracking  │    │  Protocol Treasury  │    │  Price Protection   │
│   & LP Rewards      │    │   & Fee Collection  │    │  & Impact Controls  │
└─────────────────────┘    └─────────────────────┘    └─────────────────────┘
```

### Contract Architecture

The SatoshiSwap protocol is built around a single, optimized smart contract containing several key modules:

#### 1. **Pool Management System**

- **Liquidity Pools**: Core AMM pools with automated market-making logic
- **Pool Factory**: Dynamic pool creation and initialization
- **Reserve Management**: Real-time reserve tracking and updates

#### 2. **Trading Engine**

- **Swap Execution**: Bidirectional token swapping with price calculation
- **Price Discovery**: Automated price determination based on reserve ratios
- **Slippage Protection**: Built-in safeguards against excessive slippage

#### 3. **Liquidity Provider System**

- **Position Tracking**: Individual LP position management
- **Share Calculation**: Proportional ownership calculation
- **Reward Distribution**: Automated fee distribution to LPs

#### 4. **Risk Management**

- **Price Impact Controls**: Maximum 2% price impact limitation
- **MEV Protection**: Front-running resistant execution
- **Validation Layer**: Multi-tier input validation and safety checks

## Technical Specifications

### Constants & Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| `PRECISION_MULTIPLIER` | 10,000 | Decimal precision for calculations |
| `MINIMUM_LIQUIDITY` | 1,000 | Minimum liquidity required for pools |
| `MAX_PRICE_IMPACT_BASIS_POINTS` | 200 | Maximum 2% price impact allowed |
| `DEFAULT_TRADING_FEE` | 30 | Default 0.30% trading fee |

### Data Structures

#### Liquidity Pool

```clarity
{
  token-a: principal,           // First token contract address
  token-b: principal,           // Second token contract address
  reserve-a: uint,              // Reserve amount of token A
  reserve-b: uint,              // Reserve amount of token B
  total-liquidity-shares: uint, // Total LP shares outstanding
  trading-fee: uint,            // Pool-specific trading fee
  last-update-block: uint,      // Last update block height
}
```

#### LP Position

```clarity
{
  shares: uint,                 // LP shares owned
  entry-block: uint,           // Block height when position created
}
```

## Data Flow

### 1. Pool Initialization

```
User Request → Token Validation → Initial Liquidity Transfer → Pool Creation → LP Share Assignment
```

### 2. Liquidity Provision

```
LP Request → Pool Validation → Token Transfers → Share Calculation → Position Update → Reserve Update
```

### 3. Token Swapping

```
Swap Request → Price Calculation → Slippage Check → Price Impact Validation → Token Transfer → Reserve Update → Fee Collection
```

### 4. Liquidity Withdrawal

```
Withdrawal Request → Position Validation → Share Calculation → Token Transfers → Position Update → Reserve Update
```

## Core Functions

### Pool Management

- `initialize-pool`: Create new trading pairs with initial liquidity
- `get-pool-info`: Retrieve pool state and statistics
- `get-protocol-stats`: Access protocol-wide metrics

### Liquidity Operations

- `provide-liquidity`: Add liquidity to existing pools
- `withdraw-liquidity`: Remove liquidity and claim underlying tokens
- `get-lp-position`: Query individual LP positions

### Trading Operations

- `execute-swap-a-to-b`: Swap token A for token B
- `execute-swap-b-to-a`: Swap token B for token A
- `calculate-swap-result`: Preview swap outcomes before execution

### Administrative Functions

- `update-global-fee`: Adjust protocol-wide fee parameters (owner only)

## Security Features

### Price Protection

- **Maximum Price Impact**: Prevents trades that would cause excessive price movement (>2%)
- **Slippage Guards**: User-defined minimum output protection
- **Reserve Validation**: Continuous reserve ratio monitoring

### MEV Resistance

- **Atomic Execution**: Single-block transaction finality
- **Fair Pricing**: Mathematical price discovery without oracle dependency
- **Front-running Protection**: Deterministic execution order

### Access Control

- **Owner Privileges**: Limited to fee parameter updates only
- **Decentralized Operations**: No admin control over user funds
- **Permissionless Trading**: Open access to all protocol functions

## Error Handling

The protocol implements comprehensive error handling with specific error codes:

| Error Code | Description |
|------------|-------------|
| `u1000` | Unauthorized operation |
| `u1001` | Insufficient balance |
| `u1002` | Pool already exists |
| `u1003` | Pool not found |
| `u1004` | Invalid token pair |
| `u1005` | Zero liquidity provided |
| `u1006` | Excessive price impact |
| `u1007` | Minimum output not met |
| `u1008` | Slippage exceeded |
| `u1009` | Invalid pool ID |

## Getting Started

### Prerequisites

- Stacks blockchain development environment
- Clarinet CLI tool
- Compatible SIP-010 fungible tokens

### Installation

1. Clone the repository:

```bash
git clone https://github.com/daniel-joshdev/satoshi-swap.git
cd satoshi-swap
```

2. Install dependencies:

```bash
npm install
```

3. Run tests:

```bash
npm test
```

4. Deploy to testnet:

```bash
clarinet deploy --testnet
```

### Basic Usage

#### Creating a Pool

```clarity
(contract-call? .satoshi-swap initialize-pool
  .token-a-contract
  .token-b-contract
  u1000000  ;; Initial amount A
  u2000000  ;; Initial amount B
)
```

#### Adding Liquidity

```clarity
(contract-call? .satoshi-swap provide-liquidity
  u0        ;; Pool ID
  .token-a-contract
  .token-b-contract
  u500000   ;; Amount A
  u1000000  ;; Amount B
  u100      ;; Minimum shares
)
```

#### Executing a Swap

```clarity
(contract-call? .satoshi-swap execute-swap-a-to-b
  u0        ;; Pool ID
  .token-a-contract
  .token-b-contract
  u100000   ;; Input amount
  u90000    ;; Minimum output
)
```

## Mathematical Foundation

SatoshiSwap uses the constant product formula for automated market making:

```
x * y = k
```

Where:

- `x` = Reserve of token A
- `y` = Reserve of token B  
- `k` = Constant product (invariant)

### Price Calculation

The price of token A in terms of token B:

```
Price_A = Reserve_B / Reserve_A
```

### Output Amount Calculation

For a swap of `Δx` tokens A:

```
Δy = (y * Δx * (1 - fee)) / (x + Δx * (1 - fee))
```

## Contributing

We welcome contributions to the SatoshiSwap protocol. Please read our [Contributing Guidelines](CONTRIBUTING.md) and submit pull requests to the `develop` branch.

### Development Workflow

1. Fork the repository
2. Create a feature branch
3. Implement changes with tests
4. Run the test suite
5. Submit a pull request

## Testing

Run the comprehensive test suite:

```bash
# Run all tests
npm test

# Run contract checks
clarinet check

# Run specific test file
npx vitest tests/satoshi-swap.test.ts
```

## Audit & Security

The SatoshiSwap protocol prioritizes security and has implemented multiple layers of protection:

- Comprehensive input validation
- Mathematical precision safeguards
- MEV resistance mechanisms
- Price impact limitations
- Extensive test coverage

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For technical support and questions:

- Create an issue on GitHub
- Join our Discord community
- Follow us on Twitter [@SatoshiSwap](https://twitter.com/SatoshiSwap)

## Disclaimer

SatoshiSwap is experimental software. Users should exercise caution and conduct their own research before interacting with the protocol. The developers are not responsible for any potential losses.

---

Built with ❤️ for the Bitcoin ecosystem on Stacks blockchain.
