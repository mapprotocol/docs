# CLAUDE.md - Docs

## Project Overview

This is the user documentation repository for MAP Protocol, targeting **general users and application developers** with overall introduction, operational guides, API references, and node operation instructions.

## Target Audience

- Users wanting to learn about MAP Protocol
- Node operators (Validators, Maintainers)
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
│   ├── tokenomics.md            # Tokenomics
│   ├── dao.md                   # DAO Governance
│   └── faq.md                   # FAQ
│
├── network/                     # Network Information
│   ├── relay-chain.md           # MAP Relay Chain Info
│   ├── v2-contracts.md          # 2.0 Contract Addresses
│   └── v1-contracts.md          # 1.0 Contract Addresses
│
├── run-node/                    # Run Node (Atlas)
│   ├── overview.md              # Overview
│   ├── install.md               # Installation Guide
│   └── node-types.md            # Node Types (Full/Archive/RPC)
│
├── validator/                   # Validator Guide
│   ├── become-validator.md      # Become a Validator (includes advanced setup)
│   ├── vote.md                  # Voting
│   ├── withdraw.md              # Withdrawal
│   └── marker/                  # Marker Tool
│       ├── overview.md          # Tool Overview
│       ├── common.md            # Common Commands
│       ├── validator.md         # Validator Commands
│       └── vote.md              # Vote Commands
│
├── compass-tss/                 # Compass-TSS Operation Guide (v2)
│   ├── overview.md              # Compass-TSS Introduction
│   ├── requirements.md          # Hardware/Network Requirements
│   └── register-maintainer.md   # Register as Maintainer
│
├── develop/                     # Development Guide
│   ├── overview.md              # Development Overview
│   ├── integrate-exchange.md    # Exchange Integration
│   ├── cross-chain-app/         # Cross-chain App Development
│   │   ├── overview.md          # MOS Overview
│   │   ├── asset-cross-chain.md # Asset Cross-chain Guide
│   │   ├── message-cross-chain.md # Message Cross-chain Guide
│   │   └── examples/            # Examples
│   │       └── omni-app.md      # Build OmniApp
│   └── services/                # Third-party Services
│       └── supra.md             # Supra Oracle
│
├── api/                         # API Reference
│   └── json-rpc/                # JSON-RPC API
│       ├── standard-rpc.md      # Standard Ethereum RPC
│       ├── consensus-rpc.md     # Consensus RPC
│       └── tss-rpc.md           # TSS RPC
Cross
│       └── tss-cross-rpc.md           # TSS-Cross RPC
│
├── sdk/                         # SDK
│   └── cross-chain.md           # Cross-chain SDK
│
└── resources/                   # Resources
    ├── glossary.md              # Glossary
    ├── links.md                 # Related Links
    └── changelog.md             # Changelog
```

## Version Description

### Compass-TSS (v2)
- Current active cross-chain solution
- Supports TSS-signing Maintainer
- Participates in KeyGen/KeySign processes
- Requires registration as Maintainer to participate

### Light Client (v1)
- Legacy solution (documentation in developer-docs)
- Supports Maintainer role: maintains light client state
- Supports Messenger role: delivers cross-chain messages

## Content Scope

| Content Type | This Repo | developer-docs |
|-------------|-----------|----------------|
| Project Introduction | ✅ | ❌ |
| Node Operation | ✅ | ❌ |
| Compass-TSS Deployment | ✅ | ❌ |
| Validator Operations | ✅ | ❌ |
| API/SDK Usage | ✅ | ❌ |
| Cross-chain App Development | ✅ | ❌ |
| Protocol Design Principles | Brief | ✅ Deep |
| Contract Design/ABI | ❌ | ✅ |
| Light Client Implementation | ❌ | ✅ |

## Writing Guidelines

1. **Language**: English
2. **Depth**: For general users, focus on operational steps and practicality
3. **Format**: Markdown (GitBook compatible)
4. **Examples**: Provide clear command-line examples and screenshots

## Related Repositories

- **developer-docs**: Technical documentation for advanced developers (protocol design, contract ABI, light client implementation)
- **atlas**: MAP Relay Chain source code
- **compass-tss**: Compass TSS source code
