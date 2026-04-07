# OP Stack L2 Rollup — Deployment Example

Production-ready OP Stack L2 rollup deployment on Ethereum Sepolia testnet. Includes automated setup scripts, Docker Compose services, and bridge/withdrawal tooling.

---

## What's Included

| Component | Description |
|---|---|
| **op-geth** | L2 execution client (archive mode) |
| **op-node** | L2 consensus / sequencer node |
| **op-batcher** | Posts L2 transaction batches to L1 |
| **op-proposer** | Submits L2 state roots to L1 |
| **op-challenger** | Challenges invalid state roots (Cannon trace) |
| **dispute-mon** | Monitors dispute games on L1 |

---

## Prerequisites

| Tool | Purpose | Install |
|---|---|---|
| Docker + Docker Compose | Run all services | [docs.docker.com](https://docs.docker.com/get-docker/) |
| `cast` (Foundry) | Derive addresses from private keys | `curl -L https://foundry.paradigm.xyz \| bash && foundryup` |
| `jq` | Parse JSON in scripts | `apt install jq` / `brew install jq` |
| `curl` | Download binaries | Pre-installed on most systems |

---

## Quick Start

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd create-l2-rollup-example
```

### 2. Configure environment

```bash
cp .example.env .env
```

Edit `.env` and fill in your values:

```env
# L1 RPC (get a free key from https://alchemy.com)
L1_RPC_URL="https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY"
L1_BEACON_URL="https://ethereum-sepolia-beacon-api.publicnode.com"

# Five separate wallets — fund each with Sepolia ETH
PRIVATE_KEY="your_deployer_private_key"
SEQUENCER_PRIVATE_KEY="your_sequencer_private_key"
BATCHER_PRIVATE_KEY="your_batcher_private_key"
PROPOSER_PRIVATE_KEY="your_proposer_private_key"
CHALLENGER_PRIVATE_KEY="your_challenger_private_key"

# Your server's public IP (run: curl ifconfig.me)
P2P_ADVERTISE_IP="your_public_ip"

# L2 Chain ID (pick any unused number)
L2_CHAIN_ID="16585"
```

> **Never commit `.env` to version control.** It is gitignored by default.

### 3. Fund your wallets

Each role needs Sepolia ETH. Get it from [sepoliafaucet.com](https://sepoliafaucet.com):

| Role | Recommended Balance |
|---|---|
| Deployer (`PRIVATE_KEY`) | 1+ ETH (contract deployment) |
| Batcher | 0.5+ ETH (posts batches to L1) |
| Proposer | 0.5+ ETH (submits state roots) |
| Challenger | 0.5+ ETH (dispute games) |
| Sequencer | 0.1+ ETH |

### 4. Download op-deployer

```bash
make download
# or
./scripts/download-op-deployer.sh
```

### 5. Deploy L1 contracts and configure rollup

```bash
make setup
```

This script will:
- Derive Ethereum addresses from your private keys
- Deploy all OP Stack L1 contracts on Sepolia via `op-deployer`
- Generate `genesis.json` and `rollup.json` for your L2
- Configure all service directories with the correct addresses

### 6. Start all services

```bash
make up
```

### 7. Verify the chain is running

```bash
make test-l2    # Test L2 RPC connection
make logs       # View all service logs
make status     # Show Docker Compose status
```

---

## Network Details

| Parameter | Value |
|---|---|
| L1 Network | Ethereum Sepolia (Chain ID: 11155111) |
| L2 Block Time | 2 seconds |
| Data Availability | Calldata (Ethereum) |
| Fault Proof | Cannon (permissioned) |
| Challenge Period | 7 days |
| L2 RPC | `http://localhost:8545` |
| L2 WebSocket | `ws://localhost:8546` |
| op-node RPC | `http://localhost:8547` |

---

## Bridging

### Deposit ETH: L1 → L2

```bash
./bridge-to-l2.sh
```

- Sends ETH from your Sepolia wallet to your L2 address
- Funds typically appear on L2 within 1–2 minutes

### Withdraw ETH: L2 → L1

```bash
./withdraw-from-l2.sh
```

> Withdrawals require a **7-day challenge period** before funds are finalized on L1.

---

## Makefile Reference

```bash
make help          # List all available commands

# Deployment
make download      # Download op-deployer binary
make setup         # Deploy L1 contracts + configure rollup

# Services
make up            # Start all Docker services
make down          # Stop all services
make restart       # Restart all services
make status        # Show running containers

# Logs
make logs          # All services
make logs-sequencer
make logs-batcher
make logs-proposer
make logs-challenger

# Testing
make test-l2       # Test L2 RPC
make test-l1       # Test L1 RPC

# Reset
make clean         # Remove containers and volumes
make reset         # Full reset — redeploys everything from scratch
```

---

## Project Structure

```
create-l2-rollup-example/
├── .example.env                  # Environment template
├── .gitignore
├── docker-compose.yml            # All 6 services
├── Makefile
├── bridge-to-l2.sh               # L1 → L2 deposit script
├── withdraw-from-l2.sh           # L2 → L1 withdrawal guide
└── scripts/
    ├── setup-rollup.sh           # Master deployment script
    └── download-op-deployer.sh   # Binary downloader
```

> The following directories are created automatically by `make setup` and are gitignored:
> `deployer/` `sequencer/` `batcher/` `proposer/` `challenger/` `dispute-mon/`

---

## Troubleshooting

**op-geth not starting**
```bash
make logs-sequencer
# Check for: "database already exists" or genesis mismatch
# Fix: make clean && make up
```

**Batcher transactions failing**
```bash
make logs-batcher
# Check: batcher wallet has enough Sepolia ETH
# Check: L1_RPC_URL is reachable
```

**op-node not syncing**
```bash
make logs-sequencer
# Check: L1_BEACON_URL is reachable
# Check: P2P_ADVERTISE_IP is your correct public IP
```

**Starting fresh**
```bash
make reset    # Removes all data and re-runs setup
```

---

## Security Notes

- Use dedicated wallets for each role — do not reuse personal wallets
- Never share or commit your `.env` file
- This setup is intended for **testnet use only**
- For production deployments, review the [OP Stack Security Model](https://docs.optimism.io/stack/security/security-model)

---

## References

- [OP Stack Documentation](https://docs.optimism.io)
- [op-deployer Guide](https://docs.optimism.io/operators/chain-operators/tools/op-deployer)
- [Running an OP Stack Chain](https://docs.optimism.io/operators/chain-operators/tutorials/create-l2-rollup)
- [Sepolia Faucet](https://sepoliafaucet.com)
- [Foundry (cast)](https://book.getfoundry.sh)
