# TwinForge
### Blockchain-Based Digital Twin Ownership Registry

**Decentralized ownership · Duplicate prevention · Provenance tracking · Consent-based transfer**

[![Ethereum](https://img.shields.io/badge/Ethereum-Sepolia_Testnet-3C3C3D?style=for-the-badge&logo=ethereum&logoColor=white)](https://sepolia.etherscan.io)
[![Solidity](https://img.shields.io/badge/Solidity-0.8.19-363636?style=for-the-badge&logo=solidity&logoColor=white)](https://soliditylang.org)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://reactjs.org)
[![Node.js](https://img.shields.io/badge/Node.js-18-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![IPFS](https://img.shields.io/badge/IPFS-Pinata-65C2CB?style=for-the-badge&logo=ipfs&logoColor=white)](https://pinata.cloud)



---

## The Problem

Software-based digital twins — simulation models of factory machines, turbines, industrial processes — are just files. Anyone can copy them, change a few parameters, and claim ownership. There is no reliable, tamper-proof way to:

- Prove who originally created a digital twin
- Track who currently owns it
- Prevent unauthorized duplication or modified copies
- Enforce royalty payments when the twin is resold

Existing solutions (Git, cloud storage, databases) are all centralized — one administrator can alter or delete records. They are trusted systems, not trustless ones.

---

## The Solution — TwinForge

TwinForge is a decentralized ownership registry built on the Ethereum blockchain. Every digital twin file gets a cryptographic fingerprint registered permanently on-chain, linked to the creator's wallet address, with no single party able to modify it afterward.

---

## Key Features

### Phase 1
- **Cryptographic Registration** — Unique hash fingerprint + owner wallet address written to Ethereum smart contract permanently
- **Dual-Fingerprint Clone Detection** — Two-stage similarity analysis combining structural schema extraction and MiniLM semantic AI model — detected modified copies at **95.2% accuracy** in testing
- **Consent-Based Ownership Transfer** — Propose-and-accept model where recipients must sign with their own wallet before ownership changes
- **Automatic Royalty Enforcement** — Smart contract enforces royalty payment to original creator on every resale — no trust required
- **IPFS File Storage** — Digital twin files pinned to IPFS via Pinata for decentralized, permanent, content-addressed storage
- **Standard Registration Template** — Every twin follows a consistent JSON schema ensuring comparable, meaningful metadata

### Phase 2
- **User Authentication** — Username-based accounts with JWT authentication, linked to MetaMask wallets
- **Community Transfer** — Search recipients by username instead of typing raw wallet addresses
- **In-App Transfer Chat** — Owner and recipient chat tied to each specific transfer proposal before signing
- **Declining Royalty Model** — Royalty reduces by 2% per resale (floor: 2%), enforced automatically by smart contract

---


## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Blockchain | Ethereum Sepolia Testnet | Immutable on-chain ownership records |
| Smart Contract | Solidity 0.8.19, Hardhat | Registry logic, transfer, royalty enforcement |
| Backend | Node.js, Express.js | API layer, file processing, similarity detection |
| Database | SQLite (better-sqlite3) | Off-chain metadata, fast queries |
| Similarity AI | all-MiniLM-L6-v2, Xenova Transformers | Semantic duplicate detection |
| Frontend | React 18, Tailwind CSS | User interface |
| Blockchain Bridge | ethers.js v6 | Frontend-to-contract communication |
| Wallet | MetaMask | User transaction signing |
| File Storage | IPFS via Pinata | Decentralized file pinning |
| Authentication | JWT, bcryptjs | User auth and password hashing |

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    FRONTEND LAYER                        │
│         React + Tailwind CSS + ethers.js v6             │
│  Register │ View Twins │ Transfer │ Chat │ MetaMask     │
└───────────────────────┬─────────────────────────────────┘
                        │ API calls + wallet signing
┌───────────────────────▼─────────────────────────────────┐
│                    BACKEND LAYER                         │
│           Node.js + Express.js + SQLite                  │
│  Hash Engine │ MiniLM AI │ JWT Auth │ Pinata IPFS       │
└───────────────────────┬─────────────────────────────────┘
                        │ Read-only verification
┌───────────────────────▼─────────────────────────────────┐
│                  BLOCKCHAIN LAYER                        │
│              Ethereum Sepolia + Solidity                 │
│  registerModel() │ initiateTransfer() │ acceptTransfer() │
│  getEffectiveRoyalty() │ Events (provenance history)    │
└─────────────────────────────────────────────────────────┘
```

### Data Storage Strategy

| Storage | What's stored | Why |
|---|---|---|
| **Ethereum** | Hash, owner, royalty, transfer count, events | Tamper-proof, permanent, decentralized |
| **SQLite** | Usernames, metadata, fingerprints, chat messages | Fast queries, no gas cost |
| **IPFS** | Actual digital twin files | Decentralized file storage, content-addressed |

---

## Smart Contract Functions

```solidity
registerModel(hash, name, version, royaltyPercentage)
// Registers a digital twin — called by user's own wallet

initiateTransfer(hash, proposedNewOwner)
// Proposes an ownership transfer — only current owner can call

acceptTransfer(hash) payable
// Accepts transfer — only proposed recipient can call
// Automatically splits royalty payment to original creator

getEffectiveRoyalty(hash)
// Returns current royalty after decline calculation
// Original % - (transferCount × 2%), minimum 2%

verifyModel(hash)
// Read ownership and registration details
```

---

## Getting Started

### Prerequisites
- Node.js v18+
- MetaMask browser extension
- Sepolia testnet ETH — get free from [sepoliafaucet.com](https://sepoliafaucet.com)

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/priya-dharshini-s/twinforge.git
cd twinforge
```

**2. Backend setup**
```bash
cd coe-backend
npm install
cp .env.example .env
# Fill in your .env values (see .env.example for required variables)
npm start
```

**3. Frontend setup**
```bash
cd frontend
npm install
cp .env.example .env
# Add your contract address and backend URL
npm start
```

**4. Open the app**

Go to `http://localhost:3000` — connect MetaMask to Sepolia testnet

### Environment Variables

**coe-backend/.env**
```
PRIVATE_KEY=
SEPOLIA_RPC_URL=
CONTRACT_ADDRESS=
JWT_SECRET=
PINATA_JWT=
FRONTEND_URL=http://localhost:3000
PORT=5000
```

**frontend/.env**
```
REACT_APP_CONTRACT_ADDRESS=
REACT_APP_BACKEND_URL=http://localhost:5000
```

---

## Testing the Application

### Standard Digital Twin Template
Download the registration template from the app's Register page, fill it in, and upload it. All twins must follow this standard JSON schema.

### Test Flow
1. Sign up with a unique username → connect MetaMask on Sepolia
2. Register a digital twin file → MetaMask signs the transaction
3. Try uploading the same file again → duplicate detected and blocked
4. Upload a modified version → similarity engine detects it
5. Open incognito window → sign up as a second user → connect different wallet
6. Propose transfer → search recipient by username → select profile
7. Chat with recipient → recipient accepts → royalty auto-paid
8. View model details → effective royalty reduced by 2%

---



## Results

| Metric | Result |
|---|---|
| Duplicate detection accuracy | 95.2% similarity score on modified file |
| Registration gas cost | 0.00085894 ETH |
| Propose transfer gas cost | 0.00029364 ETH |
| Accept transfer gas cost | 0.00021782 ETH |
| Registration time | Under 10 seconds |
| Network | Ethereum Sepolia Testnet |
| Total twins registered in testing | 5 twins across 3 wallet accounts |

---

## Future Work

- LSH/MinHash pre-filtering for O(1) similarity search scalability
- Binary and compiled digital twin format support
- Full IPFS decentralization (migrate from local SQLite to distributed storage)
- Mainnet deployment
- Mobile application

---

## Author

**Priya Dharshini S**  
Department of Aritifical Intelligence and Data Science
Chennai Institute of Technology  



---

## License

MIT License — free to use, modify, and distribute with attribution.

---


Built with dedication by Priya Dharshini S
