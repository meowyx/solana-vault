# Solana Vault

A secure Solana program built with Anchor that enables users to store and manage SOL in a program-derived account (PDA) vault. This program provides functionality to initialize, deposit, withdraw, and close vaults with proper access controls.

[![GitHub](https://img.shields.io/badge/GitHub-meowyx%2Fsolana--vault-blue)](https://github.com/meowyx/solana-vault)

## Features

- ✅ **Initialize Vault**: Create a new vault with state management using PDAs
- 💰 **Deposit SOL**: Securely deposit SOL into your personal vault
- 💸 **Withdraw SOL**: Withdraw specific amounts from your vault
- 🗑️ **Close Vault**: Close your vault and recover all remaining SOL plus rent

## Architecture

The program uses **Program Derived Addresses (PDAs)** to create unique vault accounts for each user:

- **State PDA**: Stores vault metadata (bumps) with seeds `[b"state", user.key()]`
- **Vault PDA**: Holds the actual SOL with seeds `[b"vault", vault_state.key()]`

This design ensures:
- Each user has their own isolated vault
- Vault addresses are deterministically derived
- Secure access control through PDA signing

## Prerequisites

Before you begin, ensure you have the following installed:

- [Rust](https://www.rust-lang.org/tools/install) (latest stable version)
- [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools) (v1.18.0 or later)
- [Anchor Framework](https://www.anchor-lang.com/docs/installation) (v0.32.0 or later)
- [Node.js](https://nodejs.org/) (v18 or later)
- [Yarn](https://yarnpkg.com/) package manager

## Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/meowyx/solana-vault.git
   cd solana-vault
   ```

2. **Install dependencies**:
   ```bash
   yarn install
   ```

3. **Build the program**:
   ```bash
   anchor build
   ```

4. **Run tests** (requires a local validator):
   ```bash
surfpool start

anchor test --skip-local-validator
   ```

## Usage

### Initializing a Vault

To create a new vault for a user:

```typescript
await program.methods
  .initialize()
  .accountsStrict({
    user: userPubkey,
    vaultState: vaultStatePda,
    vault: vaultPda,
    systemProgram: SystemProgram.programId,
  })
  .rpc();
```

### Depositing SOL

Deposit a specific amount of SOL into your vault:

```typescript
const depositAmount = 1 * LAMPORTS_PER_SOL; // 1 SOL

await program.methods
  .deposit(new anchor.BN(depositAmount))
  .accountsStrict({
    user: userPubkey,
    vault: vaultPda,
    vaultState: vaultStatePda,
    systemProgram: SystemProgram.programId,
  })
  .rpc();
```

### Withdrawing SOL

Withdraw a specific amount from your vault:

```typescript
const withdrawAmount = 0.5 * LAMPORTS_PER_SOL; // 0.5 SOL

await program.methods
  .withdraw(new anchor.BN(withdrawAmount))
  .accountsStrict({
    user: userPubkey,
    vault: vaultPda,
    vaultState: vaultStatePda,
    systemProgram: SystemProgram.programId,
  })
  .rpc();
```

### Closing a Vault

Close your vault and recover all remaining SOL:

```typescript
await program.methods
  .close()
  .accountsStrict({
    user: userPubkey,
    vault: vaultPda,
    vaultState: vaultStatePda,
    systemProgram: SystemProgram.programId,
  })
  .rpc();
```

## Development

### Local Development

The project uses [Surfpool](https://surfpool.run) for enhanced local development:

1. **Start Surfnet with auto-deployment**:
   ```bash
   surfpool start --watch
   ```

2. **Execute deployment runbook**:
   ```bash
   surfpool run deployment
   ```

### Testing

Run the test suite:

```bash
anchor test --skip-local-validator
```

The tests cover:
- Vault initialization
- SOL deposits
- SOL withdrawals
- Vault closure



## Program Instructions

### `initialize`

Creates a new vault for the user. The vault PDA is initialized with the minimum rent-exempt balance.

**Accounts:**
- `user` (mut, signer): The user creating the vault
- `vault_state` (init): The state account storing vault metadata
- `vault` (mut): The PDA account that will hold SOL
- `system_program`: Solana System Program

### `deposit`

Transfers SOL from the user to the vault.

**Accounts:**
- `user` (mut, signer): The user depositing SOL
- `vault` (mut): The vault PDA receiving SOL
- `vault_state`: The state account for verification
- `system_program`: Solana System Program

**Parameters:**
- `amount` (u64): Amount of lamports to deposit

### `withdraw`

Transfers SOL from the vault back to the user.

**Accounts:**
- `user` (mut, signer): The user withdrawing SOL
- `vault` (mut): The vault PDA sending SOL
- `vault_state`: The state account for verification
- `system_program`: Solana System Program

**Parameters:**
- `amount` (u64): Amount of lamports to withdraw

### `close`

Closes the vault, transferring all remaining SOL back to the user and closing the state account (returning rent).

**Accounts:**
- `user` (mut, signer): The user closing the vault
- `vault` (mut): The vault PDA to drain
- `vault_state` (mut, close): The state account to close
- `system_program`: Solana System Program


## Deployment

### Localnet

The program is configured to deploy to localnet by default. Update `Anchor.toml` for different clusters.


## Project Structure

```
solana-vault/
├── programs/
│   └── solana-vault/
│       └── src/
│           └── lib.rs          # Main program code
├── tests/
│   └── solana-vault.ts         # Test suite
├── migrations/
│   └── deploy.ts               # Deployment script
├── runbooks/                   # Surfpool runbooks
├── Anchor.toml                 # Anchor configuration
└── package.json               # Node.js dependencies
```

## Security Considerations

- ✅ Only the vault owner can withdraw from their vault
- ✅ PDA-based access control ensures secure account derivation
- ✅ All transfers use the Solana System Program for safety
- ✅ Rent is properly handled during vault closure

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the ISC License.

## Resources

- [Anchor Documentation](https://www.anchor-lang.com/)
- [Solana Documentation](https://docs.solana.com/)
- [Surfpool Documentation](https://docs.surfpool.run)

## Support

For issues and questions, please open an issue on [GitHub](https://github.com/meowyx/solana-vault/issues).

