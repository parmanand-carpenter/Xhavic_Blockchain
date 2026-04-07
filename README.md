# Xhavic Blockchain

This project was bootstrapped with [OP Stack](https://docs.optimism.io) — a custom Layer 2 blockchain secured by Ethereum Sepolia.

## Available Scripts

In the project directory, you can run:

### `docker compose up -d`

Starts all blockchain services in the background.

Open `http://27.5.6.234:8545` to connect via RPC, or add the network to MetaMask using Chain ID `16585`.

The sequencer will begin producing blocks every 2 seconds automatically.

### `docker compose down`

Stops all running services.

All chain data is preserved. Run `docker compose up -d` again to resume.

### `docker compose logs -f`

Streams live logs from all services.

You can monitor a specific service:

```bash
docker compose logs -f op-node
docker compose logs -f op-geth
docker compose logs -f batcher
docker compose logs -f proposer
```

### `docker compose ps`

Shows the status of all running services.

### `./bridge-to-l2.sh`

Deposits ETH from Ethereum Sepolia (L1) to Xhavic Blockchain (L2).

Funds typically arrive on L2 within **1–2 minutes**.

### `./withdraw-from-l2.sh`

Withdraws ETH from Xhavic Blockchain (L2) back to Ethereum Sepolia (L1).

**Note:** Withdrawals require a 7-day challenge period before funds are finalized on L1. This is a security requirement of the OP Stack fault proof system.

---

## Add Network to MetaMask

| Field | Value |
|---|---|
| Network Name | Xhavic Blockchain |
| RPC URL | `http://27.5.6.234:8545` |
| Chain ID | `16585` |
| Currency Symbol | `ETH` |

---

## Network Info

| | |
|---|---|
| **Chain ID** | 16585 |
| **RPC URL** | `http://27.5.6.234:8545` |
| **WebSocket** | `ws://27.5.6.244:8546` |
| **L1 Network** | Ethereum Sepolia (11155111) |
| **Block Time** | 2 seconds |

---

## Deployed Contracts

### L1 Contracts (Ethereum Sepolia)

| Contract | Address |
|---|---|
| OptimismPortal | `0x9efd6ea3d18dee98698150c7df369fcd2efea3be` |
| L1StandardBridge | `0xe7c9ba625ab8589657bc0e67090694fe141cbda7` |
| L1CrossDomainMessenger | `0xa4d9d09ea6e654ed14700749f1159669ec8d7533` |
| DisputeGameFactory | `0x5ce7e9d6acf3ccdc490bab6a2e5bcfad8fa45f54` |
| SystemConfig | `0xc955ecdae08fb59f2844cafa4a78cfdf513f7540` |

### L2 Contracts (Xhavic Blockchain)

| Contract | Address |
|---|---|
| L2StandardBridge | `0x4200000000000000000000000000000000000010` |
| L2CrossDomainMessenger | `0x4200000000000000000000000000000000000007` |
| L2ToL1MessagePasser | `0x4200000000000000000000000000000000000016` |
| WETH | `0x4200000000000000000000000000000000000006` |
