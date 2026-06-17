<div align="center">

<img src="https://img.shields.io/badge/Stellar-Soroban-7B2FBE?style=for-the-badge&logo=stellar&logoColor=white" />
<img src="https://img.shields.io/badge/Multi--Sig-Governance-F5A623?style=for-the-badge" />
<img src="https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge" />

# ⚡ QuorumForge

### Decentralised N-of-M maintainer governance for Stellar-funded open source projects.

**QuorumForge** is an open source multi-sig governance toolchain built on Soroban that replaces the single G-address bottleneck in Wave-funded open source organisations with a trustless, threshold-based maintainer board. No single maintainer controls the funds. No resolution is finalised without quorum.

</div>

---

## 🧭 The Problem

Managing a large pot of Stellar Community Fund Wave funds typically falls on one G-address — the "maintainer" account. This creates a dangerous centralisation:

- A single compromised keypair drains the entire fund
- A single maintainer going offline blocks all payouts
- Contributors have no on-chain visibility into how decisions are made
- DAOs and multi-person teams cannot participate as maintainers on Drips without nominating one person to hold the keys

```
Current reality:

  SCF Wave Funds
       ↓
  [One G-address]   ← single point of failure
       ↓
  Issue resolved?  →  Only one person decides
  Payout triggered? →  Only one person signs
  Funds at risk?   →  Only one person to blame
```

QuorumForge replaces that single address with an on-chain maintainer board where N-of-M signatures are required before any resolution is finalised or any funds move.

---

## 🧱 Repositories

| Repository | Description | Stack |
|---|---|---|
| [`quorumforge-contract`](https://github.com/quorumforge/quorumforge-contract) | Soroban multi-sig contract — board management, proposals, threshold signing | Rust, Soroban SDK |
| [`quorumforge-app`](https://github.com/quorumforge/quorumforge-app) | Governance dashboard — create proposals, cast votes, track quorum progress | Next.js 14, Tailwind |
| [`quorumforge-sdk`](https://github.com/quorumforge/quorumforge-sdk) | TypeScript SDK — embed QuorumForge governance into any tool or workflow | TypeScript, Node.js |

---

## 🔁 How It Works

```
┌──────────────────────────────────────────────────────────────┐
│                    QuorumForge Flow                           │
│                                                              │
│  1. Org deploys quorumforge-contract with their              │
│     maintainer board (e.g. 5 addresses, threshold 3-of-5)    │
│                                                              │
│  2. A maintainer creates a Proposal:                         │
│     "Resolve issue #42 — assign 500 USDC to contributor"     │
│                                                              │
│  3. Board members review and sign via the dashboard          │
│     or via quorumforge-sdk in their own tooling              │
│                                                              │
│  4. When N signatures are collected (e.g. 3-of-5):          │
│     → Contract auto-executes the resolution                  │
│     → Drips network is notified                              │
│     → Contributor payout is triggered                        │
│                                                              │
│  5. Full audit trail of all proposals and signatures         │
│     is permanently stored on-chain                           │
└──────────────────────────────────────────────────────────────┘
```

---

## 📦 Repository Breakdown

### [`quorumforge-contract`](https://github.com/quorumforge/quorumforge-contract)

The on-chain engine. A Soroban smart contract that manages the full lifecycle of multi-sig governance for a maintainer board.

**Core capabilities:**
- Deploy a board with any N-of-M configuration (e.g. 2-of-3, 4-of-7, 5-of-9)
- Create typed proposals: `ResolveIssue`, `TransferFunds`, `UpdateBoard`, `AddMember`, `RemoveMember`
- Collect signatures from board members with Ed25519 verification
- Auto-execute proposals when threshold is reached
- Full on-chain audit log of every proposal, signature, and execution
- Time-locked proposals with configurable expiry

**Proposal lifecycle:**
```
Created → Signing → [Quorum Reached] → Executed
                  ↘ [Expired / Rejected] → Cancelled
```

---

### [`quorumforge-app`](https://github.com/quorumforge/quorumforge-app)

A governance dashboard that gives every board member full visibility into the proposal queue and makes signing a one-click action via Freighter wallet.

**Maintainer view:**
- Board composition: all members, their signing history, active status
- Proposal queue: open proposals with signature progress bars
- One-click sign via Freighter — no CLI required
- Proposal detail: full context, current signers, time remaining
- "New Proposal" modal: create any proposal type from the UI

**Observer view (public):**
- Anyone can view the board composition and proposal history
- Full on-chain audit trail rendered as a human-readable timeline
- Useful for contributors wanting to track the status of their issue resolution

---

### [`quorumforge-sdk`](https://github.com/quorumforge/quorumforge-sdk)

A TypeScript SDK that makes it trivial to embed QuorumForge governance into GitHub Actions, CLI tools, bots, or any custom workflow.

**Example usage:**
```typescript
import { QuorumForgeClient } from 'quorumforge-sdk'

const client = new QuorumForgeClient({
  contractId: 'C...',
  network: 'mainnet',
  keypair: myKeypair
})

// Create a proposal to resolve an issue
const { proposalId } = await client.createProposal({
  type: 'ResolveIssue',
  issueNumber: 42,
  contributor: 'G...',
  amount: '500',
  asset: 'USDC'
})

// Sign a proposal
await client.signProposal({ proposalId })

// Check quorum status
const status = await client.getProposal(proposalId)
console.log(`${status.signatures}/${status.threshold} signatures collected`)
```

---

## 🚀 Getting Started

### Deploy a board (CLI via stellar-cli)
```bash
stellar contract invoke \
  --id C...YOUR_CONTRACT_ID \
  --fn initialize \
  -- \
  --admin G...YOUR_ADMIN \
  --members '["G...MEMBER1","G...MEMBER2","G...MEMBER3"]' \
  --threshold 2
```

### Use the dashboard
Visit [quorumforge.dev](https://quorumforge.dev), connect your Freighter wallet, and deploy or join a board in minutes.

### Install the SDK
```bash
npm install quorumforge-sdk
```

---

## 🤝 Contributing

All three repositories welcome contributions. Issues labelled `good first issue` are scoped and beginner-friendly. See `CONTRIBUTING.md` in each repo for local setup instructions.

Join `#quorumforge` on the Stellar Developer Discord for architecture discussions and contributor coordination.

---

## 🛡️ Security

Multi-sig contracts are security-critical infrastructure. If you discover a vulnerability in any QuorumForge repository, do not open a public issue. Email `security@quorumforge.dev` with full details. We follow responsible disclosure and will acknowledge all reports within 24 hours.

All contract code is designed to be auditable and will undergo a third-party security review before mainnet deployment.

---

## 📜 License

All QuorumForge repositories are released under the **MIT License**.

---

<div align="center">

Made with ⚡ for the Stellar ecosystem

[Dashboard](https://quorumforge.dev) · [Docs](https://docs.quorumforge.dev) · [Twitter / X](https://x.com/quorumforge) · [Discord](https://discord.gg/quorumforge)

</div>
