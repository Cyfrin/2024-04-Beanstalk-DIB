<img src="https://res.cloudinary.com/droqoz7lg/image/upload/q_90/dpr_2.0/c_fill,g_auto,h_120,w_120/f_auto/v1/company/dkpeqjkgetkxsga9v5q6?_a=BATAUVAA0" alt="Basin logo" align="right" width="120" />

# Beanstalk: Dive into Basin

## Contest Details

### Prize Pool

- Total Pool - 12,000
- H/M - $10,000
- Low - $1,000
- Community Judging - $1,000
- Starts: Monday, April 22, 2024
- Ends: Monday, April 29, 2024

### Stats

- nSLOC: 479
- Complexity Score: 945
- $/nSLOC: $25.05

## About

Basin is a composable EVM-native decentralized exchange protocol that allows for the composition of arbitrary exchange functions, network-native oracles and exchange implementations into a single liquidity pool known as a Well. In practice, Basin lowers the friction for market makers to deploy liquidity with custom orders and allows their liquidity to be used by other network-native protocols without additional trust assumptions.

A Well is a constant function AMM that allows the provisioning of liquidity into a single pooled on-chain liquidity position. Each Well is defined by its Tokens, Well Function, and Pump:

- The Tokens define the set of ERC-20 tokens that can be exchanged in the Well.
- The Well Function defines an invariant relationship between the Well's reserves and the supply of LP tokens.
  - Pumps are on-chain oracles that are updated upon each interaction with the Well.

Multi Flow Pump is an inter-block MEV manipulation resistant network-native Pump implementation for arbitrary current data in an EVM for both instantaneous and time-weighted average values.

**In particular, this audit is focused on changes to the ConstantProduct2 Well Function, the Multi Flow Pump (now v1.1.0) and library changes associated with each.** You can read the full RFC of and diff for Multi Flow v1.1.0 [here](https://github.com/BeanstalkFarms/Basin/pull/127). Notably, the Well Implementation in `src/Well.sol` and the Well factory contract in `src/Aquifer.sol` is not in-scope of this audit.

More documentation on Basin and Multi Flow here:

- [Docs](https://docs.basin.exchange/)
- [Basin Whitepaper](https://basin.exchange/basin.pdf)
- [Multi Flow Whitepaper](https://basin.exchange/multi-flow-pump.pdf)
- [Basin GitHub README](https://github.com/BeanstalkFarms/Basin)

## Actors

The only "actors" in the context of Basin are traders (takers) and liquidity providers (makers), as with any decentralized exchange. In a sense, external protocols that consume data stored in Pumps (like Multi Flow) could be considered another actor.

## Scope

The following contracts are in scope of this audit:

```js
src/
└── contracts/
    ├── functions/
    │   ├── ConstantProduct2.sol
    │   └── ProportionalLPToken2.sol
    ├── libraries/
    │   ├── LibLastReserveBytes.sol
    │   └── LibMath.sol
    └── pumps/
        └── MultiFlowPump.sol
```

## Compatibilities

Blockchains:

- Ethereum

Tokens:

- ERC-20 (Any ERC-20 tokens can be traded in Wells)

## Setup

This repository uses Foundry as a smart contract development toolchain.

See the [Foundry Docs](https://book.getfoundry.sh/) for more info on installation and usage.

```
foundryup
forge install
forge build
```

### Tests

Prior to running tests, you should set up your environment. At present this repository contains fork tests against ETH mainnet; your environment will need an `MAINNET_RPC_URL` key to run these tests. This is used in `IntegrationTestGasComparisons.sol`.

Additionally, the `--ffi` cheatcode is used to verify certain actions. Due to the arbitrary code execution nature of `--ffi`, it is advised to review the executed code prior to running.

To setup the python environment:

```
python3.10 -m venv env
source env/bin/activate
python3.10 -m pip install -r requirements.txt
```

The tests using -ffi are:

- `testFuzz_powu()`
- `testSim_capReserve_decrease()`
- `testSim_capReserve_increase()`

If errors occur during python execution, follow the steps described here: https://stackoverflow.com/a/75399269.

The code being executed is:

- `test/pumps/simulate.py`
- `test/differential/powu.py`

The main command to run the tests is:

`forge test --ffi`

_If the ffi tests are failing, you may need to add an `output` folder in the `test` directory._

To omit invariant tests:

`forge test --ffi --no-match-test invariant`

To run slither:

- `python3 -m pip install slither-analyzer`
- `slither .`

## Known Issues

All findings in the following audit reports:

- 06/16/23: [Basin Halborn Report](https://arweave.net/1IKi4iqRvu3qu_GdcLY4f3u9PgvOMvuRltU7AIXAEyA) @ [e5441fc](https://github.com/BeanstalkFarms/Basin/tree/e5441fc78f0fd4b77a898812d0fd22cb43a0af55)
- 06/16/23: [Basin Cyfrin Report](https://arweave.net/usT3ClfjHwpX32OXnh5De1aH79csX1PMoXJCghXBaps) @ [e5441fc](https://github.com/BeanstalkFarms/Basin/tree/e5441fc78f0fd4b77a898812d0fd22cb43a0af55)
- 10/05/23: [Basin Code4rena Report](https://code4rena.com/reports/2023-07-basin) @ [7e51c02](https://github.com/code-423n4/2023-07-basin/tree/7e51c025d32aff3f2456842c83cda66cda274d11)
- All bug reports from the Immunefi program listed [here](https://community.bean.money/bug-reports).

Please find additional Known Issues as Identified by LightChaser [here](https://github.com/Cyfrin/2024-04-Beanstalk-DIB/issues/2).
