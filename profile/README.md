# Smart Treasury Account

A programmable treasury on Stellar: a Soroban smart account that enforces
policy on payments, approvals, scheduled payments and recovery, so a treasury
is governed by contracts rather than by manual wallet operations.

- Website: <https://smarttreasury.io>
- Production app (mainnet): <https://smarttreasury.io/app>
- Documentation: <https://smarttreasury.io/docs>
- SDK on npm: [`sta-sdk`](https://www.npmjs.com/package/sta-sdk)

## What it does

| Guarantee                 | How                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Policy-enforced payments  | `policy_engine` allowlists assets, destinations and operations, caps single transfers, and fails closed on anything unknown             |
| Policy-version pinning    | Every approval carries the policy version it was made under; a later policy change invalidates it instead of reinterpreting it          |
| Replay protection         | Interactive payments consume a nonce exactly once; scheduled payments consume one child sequence per execution                          |
| Scheduled payments        | Intents recorded on chain with a ledger window; a relayer can execute them inside the window and cannot change what was approved        |
| Multi-signer authorization | `smart_account` is a custom account built on OpenZeppelin's Stellar libraries, with context rules deciding who can authorize what      |
| Guardian recovery         | Timelocked guardian quorum that can freeze the treasury and replace its owner and signers                                              |

## Repositories

| Repository                                                                       | Contents                                                                                                   |
| -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [`smart-contracts`](https://github.com/Smart-Treasury-Account-STA/smart-contracts) | Soroban contracts (Rust), tests, deployment scripts, and the testnet and mainnet deployment records         |
| [`sdk`](https://github.com/Smart-Treasury-Account-STA/sdk)                       | `sta-sdk`: typed reads, transaction preparation, SmartAccount authorization entries, event parsing          |
| [`dApp`](https://github.com/Smart-Treasury-Account-STA/dApp)                     | Next.js operator console and scheduled-payment relayer, deployed on Vercel                                  |
| [`docs`](https://github.com/Smart-Treasury-Account-STA/docs)                     | VitePress documentation: contracts, security model, tests, deployment, operator and testing guides, SDK     |
| [`marketing`](https://github.com/Smart-Treasury-Account-STA/marketing)           | The public website, which also fronts the app and the docs under one domain                                 |
| [`.github`](https://github.com/Smart-Treasury-Account-STA/.github)               | Organization defaults: this profile and the [security policy](https://github.com/Smart-Treasury-Account-STA/.github/blob/main/SECURITY.md) |

## Status

Live on Stellar mainnet as a beta since September 2026: a shared
`account_factory` deploys a complete treasury (six contracts) in one
transaction, and the production app operates it end to end. Addresses, WASM
hashes and every setup transaction are in
[`docs/MAINNET_DEPLOYMENT.md`](https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/MAINNET_DEPLOYMENT.md);
what exists today, and what does not yet, is on the
[status page](https://smarttreasury.io/docs/status).

The STA contracts have not been independently audited. The OpenZeppelin
Stellar libraries they build on have. Keep mainnet balances proportionate to
a beta.

Development is funded by the [Stellar Community Fund](https://communityfund.stellar.org/submissions/recmj5cqlrqKyd1Bc).

## Contributing and reporting

- Bugs and questions: the issue tracker of the repository concerned.
- Security vulnerabilities: privately, following the
  [security policy](https://github.com/Smart-Treasury-Account-STA/.github/blob/main/SECURITY.md).
  Never demonstrate on mainnet.
- Every repository uses pnpm (or Cargo), Conventional Commits, and a CI that
  runs formatting, linting, type checking, tests and the production build.
