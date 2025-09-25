# 🏦 DeFi Nexus - Bitcoin-Backed Lending Protocol

## Overview

**DeFi Nexus** is a decentralized, enterprise-grade lending protocol enabling Bitcoin holders to obtain **stablecoin liquidity** without liquidating their BTC positions. The protocol prioritizes **capital efficiency**, **system solvency**, and **robust risk management** through dynamic collateralization, liquidation protection, and real-time oracle integration.

This smart contract is implemented in **Clarity**, deployed on the **Stacks blockchain**, and designed for composability with the broader Bitcoin DeFi ecosystem.

---

## 🌐 System Architecture

### Key Components

| Component           | Description                                                                         |
| ------------------- | ----------------------------------------------------------------------------------- |
| **Borrowers**       | Users who deposit BTC as collateral and borrow stablecoins.                         |
| **Smart Contract**  | Manages loan creation, collateral deposits, interest, and liquidation logic.        |
| **Price Oracle**    | Feeds real-time BTC/USD prices for accurate collateral valuation.                   |
| **Governance Role** | Enables protocol updates and parameter changes (only accessible to contract owner). |

---

## 🔧 Contract Architecture

### Constants and Parameters

* `minimum-collateral-ratio`: Default 150% — governs loan issuance requirements.
* `liquidation-threshold`: Default 120% — below which positions are liquidated.
* `platform-fee-rate`: 1% fee (future extensibility).
* `VALID-ASSETS`: Currently supports `"BTC"` and `"STX"`.

### Core Data Maps

| Map                 | Purpose                                                                             |
| ------------------- | ----------------------------------------------------------------------------------- |
| `loans`             | Stores per-loan metadata including borrower, collateral, interest rate, and status. |
| `user-loans`        | Tracks active loan IDs per user.                                                    |
| `collateral-prices` | Stores oracle-fed asset prices.                                                     |

---

## 🔄 Data Flow

### 1. **Collateral Deposit**

```clojure
(deposit-collateral amount)
```

* Locks BTC in the system.
* Updates `total-btc-locked`.

### 2. **Loan Request**

```clojure
(request-loan collateral loan-amount)
```

* Validates platform state and collateral ratio.
* Mints a new loan entry in `loans`.
* Adds loan ID to `user-loans`.

### 3. **Loan Repayment**

```clojure
(repay-loan loan-id amount)
```

* Calculates interest owed.
* Validates repayment sufficiency.
* Marks loan as `repaid`.
* Releases collateral.

### 4. **Liquidation Trigger**

Automatically invoked via:

```clojure
(check-liquidation loan-id)
```

* Uses real-time price feed.
* If collateral ratio < threshold → marks loan as `liquidated`.

---

## 📦 Key Functions

### 🔐 Platform Admin (Governance)

| Function                                     | Description                                         |
| -------------------------------------------- | --------------------------------------------------- |
| `initialize-platform`                        | One-time setup callable only by the contract owner. |
| `update-collateral-ratio new-ratio`          | Adjust minimum collateral ratio.                    |
| `update-liquidation-threshold new-threshold` | Modify liquidation safety threshold.                |
| `update-price-feed asset new-price`          | Feed updated price data from a trusted source.      |

### 💼 Lending Operations

| Function                              | Description                                         |
| ------------------------------------- | --------------------------------------------------- |
| `deposit-collateral amount`           | Deposits BTC collateral.                            |
| `request-loan collateral loan-amount` | Issues a stablecoin loan.                           |
| `repay-loan loan-id amount`           | Repays principal + interest to retrieve collateral. |

### 🧠 Read-Only Views

| Function                   | Description                                                        |
| -------------------------- | ------------------------------------------------------------------ |
| `get-loan-details loan-id` | Retrieve full loan metadata.                                       |
| `get-user-loans user`      | Returns list of loan IDs associated with a user.                   |
| `get-platform-stats`       | Provides global state: total BTC locked, loans issued, parameters. |
| `get-valid-assets`         | Lists assets accepted as collateral.                               |

---

## ⚠️ Risk Management Features

* **Dynamic Collateralization**: Ensures all loans remain over-collateralized.
* **Real-Time Oracle Pricing**: BTC prices feed into collateral calculations and liquidation logic.
* **Automated Liquidation**: Loans falling below safety threshold are immediately liquidated.
* **Interest Calculation**: Block-based interest accounting ensures fair repayment.

---

## 🛡️ Security Considerations

* All state-changing functions gated with **validation assertions**.
* Only `CONTRACT-OWNER` can modify critical parameters (governance).
* Oracle-fed prices are externally auditable through `collateral-prices`.
* Collateral is only unlocked upon verified loan repayment.

---

## 🔄 Extensibility

The protocol is built with **future upgrades in mind**, including:

* Multi-asset collateral support (e.g., STX, sBTC).
* Flexible interest rate models.
* Integration with decentralized oracles (e.g., Chainlink, Subnets).
* Modular liquidation auctions or insurance vaults.

---

## 📘 Example Workflow

1. **Alice deposits BTC as collateral.**
2. **Alice requests a loan based on her BTC's USD value.**
3. **Price oracle updates BTC price.**
4. **If BTC falls, collateral ratio is re-evaluated.**
5. **If it drops below 120%, Alice’s position is liquidated.**
6. **Alice can repay full amount + interest to reclaim her BTC.**

---

## 🧑‍💼 Contract Owner Actions

The contract owner (deployer) can:

* Initialize the platform.
* Update risk parameters (`collateral ratio`, `liquidation threshold`).
* Feed in price updates via `update-price-feed`.

---

## ✅ Deployment Checklist

* [x] Deploy to Stacks mainnet/testnet
* [x] Configure BTC price oracle integration
* [x] Initialize platform via `initialize-platform`
* [x] Set initial BTC price using `update-price-feed`
* [x] Begin accepting deposits and loan requests

---

## 📄 License

MIT © DeFi Nexus Protocol — open-source and community-driven.
