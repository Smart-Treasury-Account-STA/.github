# Security policy

This policy applies to every repository of the Smart-Treasury-Account-STA
organization. Smart Treasury Account (STA) is a programmable treasury on
Stellar built with Soroban smart contracts; the contracts hold real value on
mainnet, so please report privately and never demonstrate on mainnet.

## Scope

| Repository                                                                   | What it contains                                                                                                                                                                       |
| ---------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`smart-contracts`](https://github.com/Smart-Treasury-Account-STA/smart-contracts) | The Soroban contracts (`smart_account`, `policy_engine`, `intent_registry`, `recovery_manager`, `transfer_adapter`, `split_adapter`, `account_factory`), deployment scripts, deployment records |
| [`dApp`](https://github.com/Smart-Treasury-Account-STA/dApp)                 | The operator console at `smarttreasury.io/app` and the scheduled-payment relayer that runs inside it                                                                                    |
| [`sdk`](https://github.com/Smart-Treasury-Account-STA/sdk)                   | `sta-sdk`, the TypeScript SDK published on npm                                                                                                                                         |
| [`docs`](https://github.com/Smart-Treasury-Account-STA/docs), [`marketing`](https://github.com/Smart-Treasury-Account-STA/marketing) | The documentation site and the public website                                                                                                                                          |

The live contract addresses and WASM hashes are recorded in
[`docs/MAINNET_DEPLOYMENT.md`](https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/MAINNET_DEPLOYMENT.md)
(mainnet) and
[`docs/TESTNET_FACTORY_DEPLOYMENT.md`](https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TESTNET_FACTORY_DEPLOYMENT.md)
(testnet). Mainnet is a beta. The STA contracts have **not** been
independently audited; the OpenZeppelin Stellar libraries they build on have.

## What we consider a vulnerability

Anything that breaks a guarantee documented in the
[security model](https://smarttreasury.io/docs/security/), in particular:

- Moving funds out of a treasury outside its configured policy: a
  non-allowlisted asset, destination or operation, an amount above the cap, or
  a payment validated under a policy version other than the one it pinned.
- Authorizing an action without the signers a context rule requires, or
  bypassing `smart_account`'s custom authorization (`__check_auth`).
- Replaying a consumed nonce or an already executed scheduled-payment child
  sequence.
- Executing a scheduled payment outside its ledger window, more times than
  approved, or with an asset, destination or amount different from the
  recorded intent.
- Skipping or shortening a timelock: guardian activation, recovery
  finalization, guardian-threshold change, adapter reconfiguration.
- Escaping the relayer's bounds: executing without the executor key, forging a
  relayer session or admin credential, or extracting `RELAYER_EXECUTOR_SECRET`
  / `RELAYER_ADMIN_TOKEN` / database credentials from the deployment.
- The dApp or the SDK building a transaction or authorization entry that
  differs from what the wallet showed the user, mishandling wallet signatures,
  or shipping a server-only secret in the client bundle.
- Supply chain: a malicious or vulnerable dependency reachable at runtime, a
  weakness in the npm publishing flow (OIDC trusted publishing), CI, or the
  Vercel configuration.

Out of scope, please report upstream instead: Stellar Core and Soroban
themselves, wallets (Freighter, xBull, other Stellar Wallets Kit modules), RPC
providers, and the OpenZeppelin Stellar libraries. Also out of scope:
testnet state resets, denial of service through the fee market, social
engineering of maintainers, and behaviors already documented as accepted
risks in
[`docs/SECURITY_REVIEW_STRICT.md`](https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/SECURITY_REVIEW_STRICT.md)
and `MAINNET_DEPLOYMENT.md` §6, such as signer management being gated by the
treasury's own context rules and a policy-less rule with several signers
requiring all of them.

## How to report

1. **Preferred:** open a private vulnerability report on the repository the
   issue lives in, so only maintainers see it:
   - contracts: <https://github.com/Smart-Treasury-Account-STA/smart-contracts/security/advisories/new>
   - dApp or relayer: <https://github.com/Smart-Treasury-Account-STA/dApp/security/advisories/new>
   - SDK: <https://github.com/Smart-Treasury-Account-STA/sdk/security/advisories/new>
2. **Do not** open a public issue, pull request or discussion containing the
   details, and **do not** demonstrate on mainnet. Reproduce on testnet, in
   the contract test suite (`cargo test --workspace`), or on a throwaway
   treasury deployed through the testnet `account_factory`.
3. If private reporting is not available to you, open a public issue titled
   "Security report, please contact me" with **no details**; a maintainer will
   reply with a private channel.

### What to include

- Network (mainnet or testnet) and the contract ids involved.
- A transaction hash or explorer link if something was submitted (testnet
  only, please).
- The entrypoint or dApp action, its arguments, and which signer authorized
  it.
- What the documented guarantee says should happen, and what happened.
- Your estimate of impact: which funds, which treasuries, what an attacker
  needs to hold.
- Wallet, browser, `sta-sdk` and `stellar-cli` versions when relevant.

## What to expect

- Acknowledgement within three business days. This is a target, not a
  service-level agreement: STA is maintained by a small team with no on-call
  rotation.
- Triage together with you, a severity assessment, and a fix developed
  privately.
- Existing treasuries run the contract code they were deployed with. A
  contract fix ships as new WASM hashes registered in `account_factory` for
  new treasuries; for treasuries already deployed, the advisory will say
  whether the mitigation is `pause`, a guardian freeze, a policy change, or
  moving funds.
- Coordinated disclosure through a GitHub Security Advisory once a fix or
  mitigation is available, within 90 days of the report at the latest, with
  credit to you unless you prefer otherwise.
- There is no bug bounty program at this time.

## Good-faith research

We will not pursue or support legal action against researchers who follow
this policy: report privately, avoid privacy violations and service
disruption, do not access or move funds that are not yours, and give us
reasonable time to respond before disclosing.
