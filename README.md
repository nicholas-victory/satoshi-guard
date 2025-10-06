# SatoshiGuard Pro

**Enterprise-grade Bitcoin cold storage orchestration for multi-signature wallets**
with **cryptographic proof-of-isolation** for signing keys that have never touched the internet.

---

## 🛡️ Overview

**SatoshiGuard Pro** is a battle-tested Clarity smart contract for institutional Bitcoin custody on [Stacks](https://stacks.co/).
It enforces strict **air-gapped key verification** through challenge–response cryptography before any key participates in multi-signature operations.
This ensures that every signing key proves its **cold storage provenance** on-chain, eliminating the risk of compromised keys being used in treasury transactions.

Built for:

* Institutional Bitcoin custodians
* DAO and treasury management systems
* Multi-signature vault infrastructure
* High-value UTXO orchestration requiring defense-in-depth

---

## ✨ Key Features

* 🔐 **Cold Key Challenge-Response Verification** – Every signing key must prove it has never touched the internet before it’s trusted.
* 🧰 **Configurable Multi-Signature Wallets** – Create wallets with flexible thresholds (e.g., 2-of-10) using only verified cold keys.
* 🪪 **Transaction Proposal & Approval Workflow** – Propose, sign, and execute transactions with threshold-based multi-sig enforcement.
* 🛑 **Emergency Controls** – Owner-only pause, key revocation, and incident response capabilities.
* 📜 **Immutable Verification Audit Trail** – Every cold key verification is timestamped and traceable on-chain.
* 🔄 **Secure Signer Rotation** – Requires current threshold approval and cold verification for new keys.
* ⚙️ **Gas-Optimized Maintenance** – Expired challenges can be cleaned up to reduce state size and execution cost.

---

## 🏗️ System Overview

At the core, SatoshiGuard Pro enforces a **two-phase cold storage proof** and **multi-signature transaction flow**:

1. **Cold Key Registration & Verification**

   * Register a public key fingerprint.
   * Generate a unique challenge message on-chain.
   * Sign the challenge offline using the air-gapped device.
   * Submit the signature to verify the key’s cold status.

2. **Multi-Signature Wallet Lifecycle**

   * Create a wallet using verified cold keys.
   * Propose a transaction specifying recipient and amount.
   * Collect signatures from verified cold keys until the threshold is met.
   * Execute the transaction once quorum is achieved.

3. **Emergency Operations**

   * Pause the contract to halt all operations.
   * Revoke compromised keys.
   * Resume operations once incident is mitigated.

---

## 🧱 Contract Architecture

```plaintext
SatoshiGuard Pro
├── Cold Storage Key Registry
│   └─ Verification metadata, last challenge height, proof state
│
├── Challenge Manager
│   └─ Challenge generation, expiry enforcement, signature validation
│
├── Multi-Signature Wallet Manager
│   ├─ Wallet signer configuration
│   ├─ Threshold enforcement
│   └─ Signer rotation
│
├── Transaction Workflow
│   ├─ Proposal creation
│   ├─ Multi-sig signature collection
│   └─ Execution with sufficient verified signatures
│
└── Emergency Controls
    ├─ Contract pause/unpause
    └─ Cold key revocation
```

---

## 🔄 Data Flow (High-Level)

```mermaid
flowchart TD

A[Register Cold Key] --> B[Generate Challenge]
B --> C[Sign Challenge Offline]
C --> D[Verify Cold Key On-chain]
D --> E[Cold Key Verified ✅]

E --> F[Create Multi-Sig Wallet]
F --> G[Propose Transaction]
G --> H[Sign Transaction (Cold Keys)]
H --> I[Threshold Met?]
I -->|Yes| J[Execute Transaction]
I -->|No| H
```

---

## 📚 Core Data Structures

| Map                    | Purpose                                                              |
| ---------------------- | -------------------------------------------------------------------- |
| `cold-storage-keys`    | Tracks registered keys, verification status, and metadata.           |
| `active-challenges`    | Stores ongoing challenge–response verification sessions.             |
| `wallet-signers`       | Holds multi-signature wallet configurations, signer list, and nonce. |
| `pending-transactions` | Tracks transaction proposals, signatures, and execution state.       |

---

## 🔐 Security Model

* ✅ **Cold Storage Enforcement** – All signing keys must pass challenge–response verification before being trusted.
* ⏱️ **Time-Bound Verification** – Challenges expire after `144` blocks (~24h), ensuring key freshness.
* ✋ **Threshold-Based Execution** – Transactions execute only if the required number of verified cold keys sign.
* 🛑 **Owner-Only Emergency Controls** – Only the contract owner can pause, unpause, or revoke keys.
* 🔁 **Nonce-Based Replay Protection** – Prevents replay attacks during wallet updates.

---

## ⚙️ Constants

| Constant           | Value  | Description                          |
| ------------------ | ------ | ------------------------------------ |
| `MAX-SIGNERS`      | `u10`  | Maximum number of signers per wallet |
| `MIN-THRESHOLD`    | `u2`   | Minimum required signatures          |
| `CHALLENGE-EXPIRY` | `u144` | Challenge expiry window (~24h)       |

---

## 🧪 Example Workflow

1. **Register a cold key:**

   ```clarity
   (contract-call? .satoshiguard-pro register-cold-key 0x02abcdef...)
   ```

2. **Generate a challenge:**

   ```clarity
   (contract-call? .satoshiguard-pro create-cold-key-challenge 0x1234abcd...)
   ```

3. **Submit signed challenge (offline signature):**

   ```clarity
   (contract-call? .satoshiguard-pro verify-cold-key-challenge 0xabcd1234... 0xSIG...)
   ```

4. **Create a 2-of-3 multi-sig wallet:**

   ```clarity
   (contract-call? .satoshiguard-pro create-multisig-wallet (list 0xA 0xB 0xC) u2)
   ```

5. **Propose a transaction:**

   ```clarity
   (contract-call? .satoshiguard-pro propose-transaction u1 'SP... u1000000)
   ```

6. **Sign the transaction:**

   ```clarity
   (contract-call? .satoshiguard-pro sign-transaction u1 0xA 0xSIG...)
   ```

7. **Execute when threshold is met:**

   ```clarity
   (contract-call? .satoshiguard-pro execute-transaction u1)
   ```

---

## 🛠️ Emergency Procedures

* **Pause all operations:**

  ```clarity
  (contract-call? .satoshiguard-pro pause-contract)
  ```

* **Unpause contract:**

  ```clarity
  (contract-call? .satoshiguard-pro unpause-contract)
  ```

* **Revoke a compromised key:**

  ```clarity
  (contract-call? .satoshiguard-pro revoke-cold-key 0xA)
  ```

---

## 📜 License

MIT © 2025 — SatoshiGuard Pro Contributors
