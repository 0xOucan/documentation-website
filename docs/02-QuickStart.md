---
sidebar_position: 2
---

# QuickStart

Deploy your EVVM virtual blockchain in minutes.

:::info[Building Services Instead?]
Want to build dApps on existing EVVM? Go to **[How to Create an EVVM Service](./06-HowToMakeAEVVMService.md)**.
:::

:::info[Want to experiment with a EVVM?]
You can start with the **[scaffold-evvm](https://github.com/EVVM-org/scaffold-evvm)**.
:::

---

## Prerequisites

- **Foundry** - [Install](https://getfoundry.sh/introduction/installation/)
- **Bun** (≥ 1.0) - [Install](https://bun.sh/)
- **Git** - [Install](https://git-scm.com/downloads)

The CLI validates these automatically.

## 1. Clone and Install

```bash
git clone --recursive https://github.com/EVVM-org/Testnet-Contracts
cd Testnet-Contracts
bun install
forge install
```

## 2. Environment Setup

```bash
cp .env.example .env
```

Edit `.env` with your values:
```bash
# Configuration for non cross-chain EVVM ================================================

# RPC URL for host blockchain to deploy non cross-chain EVVM instance
RPC_URL="http://0.0.0.0:8545"

# Configuration for EVVM registration ===================================================

# RPC URL for optional registration on Ethereum Sepolia Testnet
EVVM_REGISTRATION_RPC_URL="https://gateway.tenderly.co/public/sepolia"

# =======================================================================================
# BLOCKCHAIN EXPLORERS API KEYS
# =======================================================================================
# Etherscan v2 API Key (for contract verification on Ethereum networks)
# Get from: https://etherscan.io/apis
ETHERSCAN_API="YOUR_ETHERSCAN_API_KEY"
```

:::warning[Never Store Private Keys in .env]
Use Foundry's encrypted keystore to protect your private keys. The `.env` file should only contain RPC URLs and API keys.
:::

## 3. Import Wallet

```bash
cast wallet import defaultKey --interactive
```

This command securely encrypts and stores your private key.

:::tip[Custom Wallet Name]
You can use any name instead of `defaultKey`:
```bash
cast wallet import myWallet --interactive
```

Then specify it during deployment:
```bash
./evvm deploy --walletName myWallet
```
:::

## 4. Deploy with CLI

If you are on linux or macOS, run:
```bash
./evvm deploy
```

If you are on Windows, run on PowerShell:
```bash
.\evvm.bat deploy
```

:::info
Some systems may require `chmod +x evvm` to make the script executable.
:::


The interactive wizard will:
1. Validate prerequisites
2. Configure your EVVM (addresses, metadata, network)
3. Deploy contracts
4. Verify on block explorer
5. Register in EVVM Registry (optional)

### Configuration

**Administrator Addresses:**
- **Admin** - EVVM administrator
- **Golden Fisher** - An "Sudo" fisher for staking operations
- **Activator** - Manages epochs

```
Enter the admin address: 0x...
Enter the goldenFisher address: 0x...
Enter the activator address: 0x...
```

**Token Configuration:**
```
EVVM Name [EVVM]: My EVVM
Principal Token Name [Mate Token]: My Token
Principal Token Symbol [MATE]: MTK
```

Press Enter to accept the default values shown in brackets `[default]`, or type a new value.

:::tip[Prompts]
- `[value]` = Default. Press Enter to accept or type new value
- `(y/n)` = Required answer
:::

:::info[Advanced Metadata]
Advanced parameters (totalSupply, eraTokens, reward) are editable but use complex values. **If you don't understand their functionality, skip this step** by answering `n`. Default values work for most use cases.
:::

The CLI shows a summary before deployment. Review and confirm to proceed.

**Block Explorer Verification:**

Choose a verification method for your deployed contracts. The available options depend on your target network:

- **Etherscan v2** - Requires an Etherscan API key in your `.env` file
- **Blockscout** - The CLI will prompt you for the block explorer's main URL
- **Sourcify** - Automatically verifies on Sourcify without additional configuration
- **Custom** - Provide custom verification parameters using the `--verify` flag. Example:
  ```bash
  --verify --verifier-url <url> --verifier-api-key <api_key>
  ```
- **Skip verification** - Not recommended, as verification is crucial for transparency


## 5. Deployment Output

The deployment compiles and deploys 6 contracts:

- **Evvm** - Core virtual machine
- **NameService** - Domain system
- **Staking** - Staking and rewards
- **Estimator** - Reward calculation
- **Treasury** - Asset management
- **P2PSwap** - Token exchange

**Success Screen:**

After deployment, you'll see:
```
✓ Deployment completed successfully!

═══════════════════════════════════════
          Deployed Contracts
═══════════════════════════════════════

  ✓ Staking
    → 0x...
  ✓ Evvm
    → 0x...
  ✓ Estimator
    → 0x...
  ✓ NameService
    → 0x...
  ✓ Treasury
    → 0x...
  ✓ P2PSwap
    → 0x...
```

All contracts are automatically verified on the block explorer with links to view them.

Deployment data is saved to `broadcast/Deploy.s.sol/[chainId]/run-latest.json`


## 6. Register in Registry EVVM

After deployment, register your EVVM to get an official ID.

### During Deployment

The CLI asks if you want to register:
```
Do you want to register the EVVM instance now? (y/n): y
```

If yes, you'll receive an EVVM ID (≥ 1000) and registration will complete automatically.

### Register Later

```bash
./evvm register --evvmAddress 0x...
```

**Options:**
- `--evvmAddress <address>` - Your EVVM contract address
- `--walletName <name>` - Foundry wallet name (default: defaultKey)
- `--useCustomEthRpc` - Use custom Ethereum Sepolia RPC

**Example:**
```bash
./evvm register \
  --evvmAddress 0x3e562a2e932afd6c1630d5f3b8eb3d88a4b058c2 \
  --walletName myWallet \
  --useCustomEthRpc
```

:::warning[Critical Requirements]
**All registrations happen on Ethereum Sepolia**, regardless of where your EVVM is deployed. You need ETH Sepolia for gas fees.

Faucets: [ethglobal.com/faucet](https://ethglobal.com/faucet/), [alchemy.com/faucets](https://www.alchemy.com/faucets/ethereum-sepolia)
:::

**Registry Contract:**
- Address: `0x389dC8fb09211bbDA841D59f4a51160dA2377832`
- Network: Ethereum Sepolia
- [View on Etherscan](https://sepolia.etherscan.io/address/0x389dC8fb09211bbDA841D59f4a51160dA2377832#writeProxyContract)

- IDs 1-999: Reserved for official EVVMs deployments
- IDs ≥ 1000: Public registrations
- ID changeable within 24 hours, then permanent
:::

**Verify Registration:**
- Call `getEvvmIdMetadata(evvmID)` on Registry contract
- Call `getEvvmID()` on your EVVM contract
- Both should return consistent information

## CLI Command Reference

**Quick & simple:**
```bash
./evvm deploy            # Interactive deploy
./evvm deploy -s         # Deploy using existing config (non-interactive)
./evvm deploy -c         # Deploy a cross-chain EVVM (advanced)
./evvm register          # Register an EVVM in the registry
./evvm register -c       # Register a cross-chain EVVM (advanced)
./evvm setUpCrossChainTreasuries  # Configure cross-chain treasuries (if needed)
./evvm fulltest          # Run tests
./evvm help              # Show help
```

**Common examples:**
```bash
# Interactive deploy
./evvm deploy

# Non-interactive deploy (use saved inputs)
./evvm deploy -s

# Register an EVVM
./evvm register --evvmAddress 0x... --walletName myWallet
```

**Tip:** Import wallets securely:
```bash
cast wallet import defaultKey --interactive
```

**Support:** `https://github.com/EVVM-org/Testnet-Contracts/issues`

## Next Steps

**Documentation:**
- **[How to Make a EVVM Service](./06-HowToMakeAEVVMService.md)** - Build dApps/services on your EVVM
- **[Transaction Flow](./03-ProcessOfATransaction.md)** - Understand transaction processing
- **[Core Contracts](./04-Contracts/01-EVVM/01-Overview.md)** - EVVM, Staking, NameService, Treasury
- **[Signature Structures](./05-SignatureStructures/01-EVVM/01-SinglePaymentSignatureStructure.md)** - EIP-191 specifications
- **[Registry System](./08-RegistryEvvm/01-Overview.md)** - Registry governance and management

---

Your EVVM is ready for development.
