# 🧩 TestnetX Architecture (Merged: Marketplace + ACE + $TESTX)

## 1. Core Idea

A Testnet Marketplace and Intelligence Hub where:

- Users can buy, sell, swap, and burn testnet tokens and NFTs.  
- Users discover, research, and publish original testnet findings.  
- Contributions, trade volume, and verified activity are measured as **ACE**, a non-transferable ranking metric.  
- Platform token **$TESTX** is the utility and reward token distributed according to activity, multipliers and staking.

---

## ⚙️ 2. Updated Architecture Overview

| Layer | Description | Tools / Options |
|-------|-------------|-----------------|
| **Frontend (UI)** | User interface for listings, wallet connection, dashboard | Next.js + TailwindCSS + Wagmi + RainbowKit + Base App + Rabby |
| **Smart Contracts** | Listing, swap, escrow, NFT burn, prediction market logic | Solidity + Hardhat / Foundry + OpenZeppelin |
| **Backend / Indexer** | Tracks marketplace, submissions, ACE, $TESTX emissions | Node.js (Express) + PostgreSQL / MongoDB |
| **Blockchain Layer** | Primary on-chain execution and testnet interactions | Base Testnet / Sepolia / Monad |
| **Data Aggregation Layer** | Aggregates testnet signals, airdrops, analytics | Kaito, Zama, Airdrops.io APIs |
| **Privacy Layer** | Private computation and encrypted state handling | Zama FHEVM (Zama SDK) |
| **Reputation Layer (ACE Engine)** | Scores contributions, reputation, and tier logic | Kaito AI + on-chain scoring contracts |
| **Token Layer** | $TESTX liquidity, staking, and governance | ERC20 or similar, emissions controlled by smart contracts |
| **AI Layer (Optional)** | Recommends testnets and analyzes originality | Kaito API + OpenAI |

---

## 🧠 3. Wallet Integration

Supported wallets for onboarding and power users:

### a. Base App (Base Wallet)
- Default onboarding for new users.  
- Built-in fiat-crypto bridge.  
- Native Base chain support.

### b. Rabby Wallet
- Advanced user features.  
- Quick chain switching and DApp UX improvements.

### c. RainbowKit / Wagmi
- Unified connection experience (MetaMask, Coinbase Wallet etc).

**Example Wallet Setup Code:**

```js
import { connectorsForWallets } from '@rainbow-me/rainbowkit';
import { rabbyWallet, metaMaskWallet, baseWallet } from '@rainbow-me/rainbowkit/wallets';

// Example setup
const connectors = connectorsForWallets([
  {
    groupName: 'Recommended',
    wallets: [rabbyWallet({ chains }), metaMaskWallet({ chains }), baseWallet({ chains })],
  },
]);

export default connectors;
```
🏗️ 4. Key Functional Modules

a. User Module

Connect wallet (Base / Rabby / MetaMask)

Profile creation, SBT or soulbound record for verified identity

ACE score display, XP, badges, tier view


b. Marketplace Module

List testnet tokens and NFTs

Escrow smart contracts for trades

Swap engine and liquidity pools for testnet pairs

Burn-for-reward flows for NFTs with bridge attestation


c. Testnet Discovery & Research Module

Submit discovery reports and research posts

IPFS storage or Lens integration for content persistence

Community + AI originality scoring pipeline


d. Prediction / Event Markets Module

Create prediction events using testnet tokens

Settlement via oracles (Chainlink or custom)

Note: identity verification is anti-bot, not for enabling betting


e. Identity & Anti-Bot Module

Human verification options: zkID, Worldcoin, or facial attestations via privacy-preserving proofs

Zama FHE used to encrypt and compute verification proofs without leaking raw biometric data


f. Reputation, Rewards & ACE Engine

ACE is calculated from verified actions (swaps, burns, accuracy, research originality, LP time)

ACE is non-transferable and defines tiered multipliers for $TESTX rewards



---

🔐 5. Privacy & FHE Integration (Zama)

Zama FHEVM is used to:

Encrypt trade amounts, balances and sensitive user info while enabling on-chain logic

Compute contribution scoring or verification checks privately

Store or compute biometric verification proofs without exposing raw data


Example Solidity Snippet:

import "fhevm/lib/FHE.sol";

uint256 encryptedPrice = FHE.encrypt(0.2 ether);
uint256 decrypted = FHE.decrypt(encryptedPrice); // accessible to contracts with correct keys


---

🧾 6. ACE and $TESTX Tokenomics (High level)

ACE (ranking metric)

Non-transferable on-chain reputation.

Earned via verified actions:

Swap volume and bridges: volume-weighted ACE

NFT burns: burn-weighted ACE

Prediction accuracy: accuracy-weighted ACE

Research submissions: originality-weighted ACE (AI + community validation)

LP staking: time-weighted ACE


ACE decays slowly with inactivity to keep rankings fresh.

ACE determines tier and $TESTX multipliers.


$TESTX (platform token)

ERC20 utility / governance token

Emission sources:

Swap fees redistribution

Reward pool for verified research and curated discoveries

Liquidity mining and staking rewards


ACE multiplies $TESTX earning rate per activity

$TESTX used for: marketplace fees, staking, governance votes, and unlocking premium features



---

🚀 7. MVP Rollout Plan

Phase 1 (MVP)

Basic frontend listing UI + wallet connections (Base, Rabby)

Simple marketplace: list, buy, escrow, basic swaps on a testnet

Off-chain backend for tracking listings and basic ACE points


Phase 2

Integrate Zama FHE for private listings and encrypted balances

Research submission flow with AI originality checks

$TESTX reward mechanics and initial ACE tier system


Phase 3

Prediction engine and oracle integration

Burn-to-real-reward bridge partnerships

DAO governance and full $TESTX tokenomics rollout



---

🧰 8. No-Code / Low-Code Options (if you want to prototype fast)

Thirdweb for contract scaffolding and deployments

Moralis for wallet auth and quick APIs

Bubble.io for a frontend prototype

Replit + ChatGPT for quick contract/code generation

Zama Sandbox for testing FHE features



---

🪙 9. Supported Wallets (summary)

Base App

Rabby Wallet

MetaMask (via Wagmi / RainbowKit)

Coinbase Wallet<img width="1536" height="1024" alt="1000095369" src="https://github.com/user-attachments/assets/3845ce9d-d348-4135-899a-f8e789fe3629" />
