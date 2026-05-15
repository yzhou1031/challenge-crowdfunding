# Challenge: Crowdfunding
> Trustless ETH crowdfunding with deadline enforcement, threshold detection, and automatic refunds

[![Solidity](https://img.shields.io/badge/Solidity-0.8.20-363636?logo=solidity&logoColor=white)]()
[![Foundry](https://img.shields.io/badge/Built_with-Foundry-red)]()
[![Next.js](https://img.shields.io/badge/Frontend-Next.js-black?logo=next.js&logoColor=white)]()
[![Sepolia](https://img.shields.io/badge/Network-Sepolia-8A2BE2)]()

🔗 [Live Demo](https://nextjs-8e0o47z06-yuchenzhou1031-6631s-projects.vercel.app/) · 📋 [Speedrun Ethereum](https://speedrunethereum.com)

## What It Does

A crowdfunding dApp where contributors pool ETH toward a threshold. If the threshold is met before the deadline, funds forward automatically to the recipient contract. If not, every contributor can withdraw their exact contribution. No admin key, no platform fee, no trust required beyond the code.

## Real-World Relevance

- **Juicebox / ConstitutionDAO** — Juicebox used the same pool-then-distribute-or-refund pattern to coordinate $47M from 17,000+ contributors for a failed Constitution bid; automatic refunds were enforced by smart contract, not by any company
- **Gitcoin Grants** — quadratic funding for public goods uses on-chain pooling and distribution; the coordination problem solved here (pooling funds from strangers) is identical
- **Nouns DAO** — daily ETH auctions fund a community treasury; the on-chain conditional disbursement pattern built here underlies all DAO treasury mechanics

## Contract Architecture

| Contract | Role |
|---|---|
| `CrowdFund.sol` | Tracks per-contributor balances, enforces the deadline, forwards funds to recipient on success or opens withdrawals on failure |
| `FundingRecipient.sol` | Simple recipient with a `completed` flag; set to `true` when `complete()` is called with the full ETH amount |

## Key Concepts

- **Checks-Effects-Interactions in `withdraw()`** — balance is zeroed out before the ETH transfer to prevent re-entrancy: `balances[msg.sender] = 0` then `call{value: balance}`
- **Deadline-based state machine** — `execute()` reverts with `TooEarly` before the deadline; after it, the contract branches on whether the threshold was met
- **`receive()` as `contribute()`** — sending ETH directly to the contract triggers `contribute()`, making the contract compatible with standard wallet transfers

## Local Setup

```bash
yarn chain    # start local Anvil blockchain
yarn deploy   # deploy FundingRecipient + CrowdFund
yarn start    # frontend at http://localhost:3000
```
