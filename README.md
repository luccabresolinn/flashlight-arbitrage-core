# ⚡ Flashlight Arbitrage Core (V12)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Arbitrum](https://img.shields.io/badge/Network-Arbitrum-blue)](https://arbitrum.io/)
[![Powered By](https://img.shields.io/badge/Powered%20By-FlashlightCoin-green)](https://flashlightcoin.com)

> **Official open-source implementation of the Arbitrage Exhaustion Model used at [FlashlightCoin.com](https://flashlightcoin.com).**

## 📖 Overview
This repository contains the core logic for the **Flashlight High-Frequency Monitor**, a tool designed to scan Uniswap V3 and PancakeSwap V3 on the Arbitrum network for price inefficiencies.

Unlike standard arbitrage bots, this core utilizes the **Statistical Spread Theorem** to filter out "false positives" caused by low liquidity or gas spikes, focusing only on opportunities where the profit probability exceeds $P > 0.95$.

---

## 📐 The Mathematical Theorem
The logic within `monitor.js` is built upon the **Flashlight Execution Inequality**. For a trade to be executed, it must satisfy the following condition derived from our [Technical Documentation](https://flashlightcoin.com/documentation):

$$Profit_{net} = (V_{out} - V_{in}) - (C_{gas} + C_{flash} + \delta_{slippage}) > 0$$

Where:
* $V_{out}$: Value returned from the exit DEX (e.g., PancakeSwap).
* $V_{in}$: Initial capital borrowed via Flash Loan (e.g., Uniswap).
* $C_{gas}$: Dynamic execution cost on L2 (Arbitrum).
* $C_{flash}$: The 0.05% protocol fee for the uncollateralized loan.
* $\delta_{slippage}$: Estimated price impact based on liquidity depth.

### Z-Score Integration
While this repository handles the *execution*, the *signal* validation relies on the **Z-Score Normal Distribution** model. We look for price deviations of $\sigma \ge 2.5$ from the mean VWAP (Volume Weighted Average Price).
> *Learn more about our Z-Score logic here: [FlashlightCoin Research](https://flashlightcoin.com/documentation)*

---

## 🚀 How It Works (Code Logic)
The `monitor.js` script operates in three atomic stages to ensure sub-second execution:

1.  **Multicall Scanning:** Uses `aggregate3` to fetch price quotes from Uniswap and PancakeSwap simultaneously in a single RPC call, reducing latency by 40ms.
2.  **Spread Calculation:** Immediately calculates the spread percentage. If `spread > 0.20%` (20 basis points), it triggers the secondary verification.
3.  **Liquidity Verification:** Simulates the trade using `staticCall` to verify the exact output amount ($V_{out}$) before broadcasting any transaction, preventing failed gas fees.

## 🛠️ Installation & Usage

### Prerequisites
* Node.js v16+
* An Arbitrum RPC URL (Alchemy/Infura)

### Setup
1. Clone the repository:
   ```bash
   git clone [https://github.com/YOUR-USERNAME/flashlight-arbitrage-core.git](https://github.com/YOUR-USERNAME/flashlight-arbitrage-core.git)
