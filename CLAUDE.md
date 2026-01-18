# CLAUDE.md - Docs

## Project Overview

This is the user documentation repository for MAP Protocol, targeting **general users and application developers** with overall introduction, operational guides, API references, and node operation instructions.

## Target Audience

- Users wanting to learn about MAP Protocol
- Node operators (Validators, Maintainers, Messengers)
- DApp developers (using API/SDK for cross-chain application development)
- Exchange integration personnel

## Documentation Structure

```
docs/
├── learn/                       # Learn MAP Protocol
│   ├── introduction.md          # Project Introduction
│   ├── architecture/            # Architecture Introduction
│   │   ├── overview.md          # Three-layer Architecture Overview
│   │   ├── v1-light-client.md   # v1 Solution Brief
│   │   └── v2-tss.md            # v2 Solution Brief
│   ├── comparison.md            # Comparison with Other Solutions
│   ├── tokenomics.md            # Tokenomics
│   ├── dao.md                   # DAO Governance
│   └── faq.md                   # FAQ
│
├── quick-start/                 # Quick Start
│   ├── overview.md
│   ├── for-users.md             # For Users
│   ├── for-developers.md        # For Developers
│   └── for-validators.md        # For Validators
│
├── run-node/                    # Run Node (Atlas)
│   ├── overview.md
│   ├── requirements.md          # Hardware Requirements
│   ├── install.md               # Installation Guide
│   ├── run-full-node.md         # Run Full Node
│   ├── run-archive-node.md      # Run Archive Node
│   ├── run-rpc-node.md          # Run RPC Node
│   └── troubleshooting.md       # Troubleshooting
│
├── validator/                   # Validator Guide
│   ├── overview.md
│   ├── become-validator.md      # Become a Validator
│   ├── advanced-setup.md        # Advanced Setup
│   ├── vote.md                  # Voting
│   ├── withdraw.md              # Withdrawal
│   └── marker-tool.md           # Marker Tool Usage
│
├── compass/                     # Compass Operation Guide (v1)
│   ├── overview.md              # Compass Introduction
│   ├── install.md               # Installation
│   ├── config.md                # Configuration
│   ├── run-maintainer.md        # Run Maintainer
│   ├── run-messenger.md         # Run Messenger
│   └── troubleshooting.md       # Troubleshooting
│
├── compass-tss/                 # Compass-TSS Operation Guide (v2)
│   ├── overview.md              # Compass-TSS Introduction
│   ├── requirements.md          # Hardware/Network Requirements
│   ├── install.md               # Installation
│   ├── config.md                # Configuration
│   ├── run.md                   # Running
│   ├── register-maintainer.md   # Register as Maintainer
│   └── troubleshooting.md       # Troubleshooting
│
├── develop/                     # Development Guide (Application Layer)
│   ├── overview.md
│   ├── cross-chain-app/         # Cross-chain App Development
│   │   ├── getting-started.md
│   │   ├── mos-message.md       # Using MOS Message
│   │   └── examples/            # Examples
│   ├── smart-contracts/         # Smart Contract Development
│   │   ├── getting-started.md
│   │   ├── deploy.md
│   │   └── verify.md
│   └── integrate-exchange.md    # Exchange Integration
│
├── api/                         # API Reference
│   ├── overview.md
│   ├── json-rpc/                # JSON-RPC API
│   │   ├── standard-rpc.md      # Standard Ethereum RPC
│   │   └── consensus-rpc.md     # Consensus RPC
│   ├── mos-api.md               # MOS Interface
│   ├── scan-api.md              # Block Explorer API
│   └── bridge-api.md            # Cross-chain Bridge API
│
├── sdk/                         # SDK
│   ├── overview.md
│   ├── javascript.md
│   ├── go.md
│   └── butter.md                # Butter SDK
│
├── network/                     # Network Information
│   ├── mainnet.md               # Mainnet Info
│   ├── testnet.md               # Testnet Info
│   ├── supported-chains.md      # Supported Chains
│   └── contract-addresses.md    # Contract Addresses
│
└── resources/                   # Resources
    ├── glossary.md              # Glossary
    ├── links.md                 # Related Links
    └── changelog.md             # Changelog
```

## Version Description

### Compass (v1)
- Supports Maintainer role: maintains light client state
- Supports Messenger role: delivers cross-chain messages

### Compass-TSS (v2)
- Supports TSS-signing Maintainer
- Participates in KeyGen/KeySign processes
- Requires registration as Maintainer to participate

## Content Scope

| Content Type | This Repo | developer-docs |
|-------------|-----------|----------------|
| Project Introduction | ✅ | ❌ |
| Node Operation | ✅ | ❌ |
| Compass/Compass-TSS Deployment | ✅ | ❌ |
| Validator Operations | ✅ | ❌ |
| API/SDK Usage | ✅ | ❌ |
| Cross-chain App Development Tutorial | ✅ | ❌ |
| Protocol Design Principles | Brief | ✅ Deep |
| Contract Design/ABI | ❌ | ✅ |

## Writing Guidelines

1. **Language**: English
2. **Depth**: For general users, focus on operational steps and practicality
3. **Format**: Markdown
4. **Examples**: Provide clear command-line examples and screenshots

## Related Repositories

- **developer-docs**: Technical documentation for advanced developers (protocol design, contract ABI, etc.)
- **atlas**: MAP Relay Chain source code
- **compass**: Compass v1 source code
- **compass-tss**: Compass TSS source code
