# Escrow Program

A Solana smart contract (program) for secure, trustless escrow of SPL tokens, built with the Anchor framework.

## Table of Contents
- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
- [Account Structure](#account-structure)
- [Instruction Flow](#instruction-flow)
- [Getting Started](#getting-started)
- [Directory Structure](#directory-structure)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

---

## Overview
This program enables two parties to exchange tokens securely using an escrow mechanism. Funds are only released when both parties fulfill their obligations, ensuring atomic and trustless swaps. The program is written in Rust using the [Anchor](https://book.anchor-lang.com/) framework for Solana.

## Features
- Trustless escrow for SPL tokens
- Atomic swaps: all-or-nothing execution
- Permissionless: anyone can create or participate
- Refund and cancellation support
- Built with Anchor for safety and developer ergonomics
- Supports custom token mints and amounts
- On-chain state for escrow tracking

## How It Works
1. **Make**: The maker initializes an escrow, locking their tokens in a vault and specifying the amount they expect in return.
2. **Take**: The taker accepts the escrow, providing the expected tokens. The program atomically swaps the tokens between the parties.
3. **Refund**: If the escrow is not taken, the maker can reclaim their tokens.

## Account Structure
- **Escrow Account**: Stores escrow state (maker, token mints, expected amounts, bump, etc.)
- **Vault Account**: Associated Token Account (ATA) owned by the escrow, holds the maker's deposited tokens
- **Maker/Taker Token Accounts**: Standard ATAs for each party

### Escrow Account Fields
- `seed`: Unique identifier for the escrow
- `maker`: Public key of the escrow creator
- `mint_a`: Token mint the maker is depositing
- `mint_b`: Token mint the maker expects in return
- `receive`: Amount of `mint_b` expected from taker
- `bump`: PDA bump for escrow account

## Instruction Flow
### 1. Make
- Maker creates an escrow specifying:
  - `seed` (unique u64)
  - `deposit` (amount of `mint_a` to lock)
  - `receive` (amount of `mint_b` expected)
- Program creates the escrow account and vault, transfers `deposit` tokens from maker to vault

### 2. Take
- Taker provides the expected `mint_b` tokens
- Program transfers `mint_b` from taker to maker, and `mint_a` from vault to taker
- Vault is closed and rent returned

### 3. Refund
- If no taker, maker can reclaim their tokens
- Program transfers all `mint_a` from vault back to maker and closes the vault

## Getting Started

### Prerequisites
- [Rust](https://www.rust-lang.org/tools/install)
- [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools)
- [Anchor](https://book.anchor-lang.com/chapter_1/installation.html)
- Node.js (for tests)

### Build & Deploy
1. Clone the repository:
   ```sh
   git clone <your-repo-url>
   cd escrow
   ```
2. Install dependencies:
   ```sh
   yarn install
   ```
3. Build the program:
   ```sh
   anchor build
   ```
4. Deploy to localnet:
   ```sh
   anchor deploy
   ```

### Example Usage (Anchor CLI)
- To run tests and see example flows:
  ```sh
  anchor test
  ```
- To interact manually, use the generated IDL and Anchor client scripts.

## Directory Structure
- `programs/escrow/src/lib.rs`: Main program logic and entrypoints (`make`, `take`, `refund`)
- `programs/escrow/src/instructions/`: Instruction handlers for each action
- `programs/escrow/src/state/`: State definitions (e.g., `Escrow` account)
- `tests/escrow.ts`: Anchor-based integration tests
- `migrations/deploy.ts`: Anchor deploy script
- `Anchor.toml`: Anchor configuration
- `package.json`: JS/TS dependencies for tests

## Testing
Run tests with:
```sh
anchor test
```

- Tests are written in TypeScript using Mocha/Chai and Anchor's testing framework.
- See `tests/escrow.ts` for example flows.

## Troubleshooting
- Ensure Solana CLI and Anchor are installed and on your PATH
- If you see build errors, try `anchor clean` and rebuild
- For localnet issues, restart with `solana-test-validator`

## Contributing
Contributions are welcome! Please open issues or submit pull requests.

## License
MIT License. See [LICENSE](LICENSE) for details.
