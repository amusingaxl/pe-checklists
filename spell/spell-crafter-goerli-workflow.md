# Spell Crafter Goerli Workflow

## Goerli

PR: https://github.com/makerdao/spells-goerli/pull/<TODO>

### [Governance Cadence Improvement](https://forum.makerdao.com/t/governance-cadence-improvement/14972)

![](https://ipfs.io/ipfs/QmUqCvy7c8Qmzn7yZ6D3353wTqCZ3VDAwQKYB37pJ2BjXb)

### Steps:
* [ ] Create a new branch on the `spells-goerli` repo named `YYYY-MM-DD` using the initial target date of the spell
  * [ ] Ensure the same target date is used as the corresponding `spells-mainnet` spell branch
* [ ] Pull `master` Locally and Checkout Branch (IF Branch is created via GitHub)
* [ ] Pull `master` Locally, Create and Checkout Branch (IF Branch was not created via GitHub)
* [ ] Cleanup Previous Spell's Actions in `DssSpell.sol`
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
  * [ ] Delete unused dependencies in the `src/dependencies` folder where applicable
* [ ] Cleanup `src/test/config.sol`
  * [ ] Set `deployed_spell` to `address(0)`
  * [ ] Set `deployed_spell_created` to `0`
  * [ ] Set `deployed_spell_block` to `0`
* [ ] Cleanup Specific Tests in `DssSpell.t.sol`
  * [ ] Check previous spells in the `archive` folder for cleanup patterns
  * [ ] Disable specific tests IF Not Used (e.g. `testCollateralIntegrations`, `testNewChainlogValues`, `testNewIlkRegistryValues`, ...)
    * [ ] Remove spell-specific part
    * [ ] Keep setup
    * [ ] Disable by setting visibility to `private`
    * [ ] Add commented notes
      * [ ] e.g. `// Insert new collateral integration tests here`
    * [ ] Keep commented tests (e.g. `testOSMs`, `testMedianizers`)
* [ ] Add comments to the spell:
  * [ ] Copy every _Section text_ from the Exec Sheet as comment to the spell code (above the code segment that implements the action)
    * [ ] Surround the comment by the set of dashes (E.g. `// ----- Section text -----`)
  * [ ] For every _Instruction text_ Exec Sheet:
    * [ ] copy to the spell code as `// Instruction text`
    * [ ] add newline above it
  * [ ] For every `Reasoning URL` and `Authority URL` from the Exec Sheet add comment with it under relevant section or instruction in the spell code (depending on which row the link is present)
    * [ ] For every `Reasoning URL` and `Authority URL` add prefix derived from the url itself
      * [ ] `// Executive Vote:` if URL starts with `https://vote.makerdao.com/executive/`
      * [ ] `// Poll:` if URL starts with `https://vote.makerdao.com/polling/`
      * [ ] `// Forum:` if URL starts with `https://forum.makerdao.com/t/`
      * [ ] `// MIP:` if URL starts with `https://mips.makerdao.com/mips/details/`
  * [ ] If action in the spell doesn't have relevant instruction (e.g.: `chainlog` version bump), add the explanation prefixed with `// Note:`
  * [ ] If an instruction can not be taken, add a comment under the instruction prefixed with `// Note:` (e.g.: `// Note: Payments are skipped on goerli`)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Ensure to use latest `foundry` stable version
    * [ ] Run `foundryup`
* [ ] Tests PASS via `make test`
* [ ] Commit & Push `Base spell`
* [ ] CI Tests PASS
* [ ] Open Draft PR on `spells-goerli` titled `YYYY-MM-DD Goerli Spell` where `YYYY-MM-DD` is the target date of the spell
* [ ] Assign to yourself
* [ ] BECAUSE corresponding Exec Doc is NOT ready
  * [ ] Add Spell Actions as per [Exec Sheet](https://docs.google.com/spreadsheets/d/1w_z5WpqxzwreCcaveB2Ye1PP5B8QAHDglzyxKHG3CHw) - [Polls](https://vote.makerdao.com/polling?network=mainnet)
  * [ ] Polls starts on Monday and ends on Thursday
    * [ ] Ensure spell actions match polls details and links (forum posts, MIPs portal, ...)
    * [ ] Add a comment for the polling detail URL
          `// https://vote.makerdao.com/polling/<hash>`
    * [ ] Add a comment for the forum post URL
          `// https://forum.makerdao.com/t/<title>/<number>`
  * [ ] Check on `new-spells` discord channel when Exec Doc is ready
  * [ ] Pragma
    * [ ] Current solc version `0.8.16`
  * [ ] Interfaces
    * [ ] Consider using `DssExecLib` actions where possible (to avoid introducing interfaces where not required)
    * [ ] Avoid `dss-interfaces` multi-import layout (see [issue #69](https://github.com/makerdao/dss-interfaces/issues/69))
    * [ ] Prefer single import layout
      * [ ] `import { VatAbstract } from "dss-interfaces/dss/VatAbstract.sol";`
    * [ ] Consider static interfaces
      * [ ] IF not present in `dss-interfaces`
      * [ ] IF present in `dss-interfaces` but TOFIX
      * [ ] IF only a few function interfaces are needed
  * [ ] Office Hours (OFF by Default on Goerli)
    * [ ] Ensure `officeHours` modifier is overridden to return `false`
  * [ ] Deploy New Join, Clip and Calc (Onboarding)
    * [ ] `Join` (check which one is required)
      * [ ] Use [JoinFab](https://github.com/makerdao/JoinFab) to deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
        * [ ] IF Flatten, consider removing `HEVM` interface artifacts
    * [ ] `Clip`
      * [ ] Use [ClipFab](https://github.com/makerdao/dss-deploy) to deploy
      * [ ] Etherscan Verification
        * [ ] Make sure AGPLv3 is specified and used
    * [ ] `Calc` (check which one is required)
      * [ ] Use [CalcFab](https://github.com/makerdao/dss-deploy) to deploy
      * [ ] Note: automatically verified on etherscan
    * [ ] Check with Oracle CU
      * [ ] Check IF oracle deployment is required (e.g. univ3-lp-oracle, new ilk pip, ...)
  * [ ] Ensure every spell variable is declared as public/internal
  * [ ] Consider `immutable` visibility when fetching addresses from the `ChainLog` via `DssExecLib.getChangelogAddress` and use `constant` for static addresses
    * [ ] Fetch addresses as type `address` and wrap with `Like` suffix interfaces inline (when making calls) unless archive patterns permit otherwise (Such as `MKR`)
    * [ ] Use the [DssExecLib Core Address Helpers](https://github.com/makerdao/dss-exec-lib/blob/master/src/DssExecLib.sol#L166) where possible (e.g. `DssExecLib.vat()`)
    * [ ] Where addresses are fetched from the `ChainLog`, the variable name must match the value of the ChainLog key for that address (e.g. `MCD_VAT` rather than `vat`), except where the archive pattern differs from this pattern (e.g. MKR)
  * [ ] Add New Addresses in the ChainLog
  * [ ] Bump ChainLog, accordingly with spec (`major`, `minor`, `patch`)
    * [ ] MAJOR -> New Vat
    * [ ] MINOR -> Core Module (DSS) Update (e.g. Flapper)
    * [ ] PATCH -> Collateral addition or addition/modification
  * [ ] Use DssExecLib Functions
    * [ ] Ensure `DssExecLib` address used in current spell (`DssExecLib.address`) matches `dss-exec-lib` [Latest Release Tag](https://github.com/makerdao/dss-exec-lib/releases/latest)
    * [ ] Check previous spells for common patterns
* [ ] Adjust System Values, Collateral Values in `config.sol` (i.e. diffcheck via vscode `code --diff config1.sol config2.sol`) with `spells-mainnet`)
* [ ] Add Specific Tests in `DssSpell.t.sol` (i.e. diffcheck via vscode `code --diff source1.sol source2.sol`) with `spells-mainnet`)
  * [ ] Add new collateral tests
  * [ ] Add new chainLog value tests
  * [ ] Add new ilk registry value tests
  * [ ] Add specific tests (DAI/MKR Streams/Payments, Lerps, ...)
* [ ] Add new ChainLog Address in `addresses_goerli.sol` (e.g. Collateral Onboarding)
* [ ] Run Tests `make test` or `make test match=<test_name>` to inspect debug traces
  * [ ] Ensure Good Coverage
  * [ ] Ensure every test function is declared as public if enabled or private if disabled
  * [ ] Tests PASS via `make test`
* [ ] Open PR & Add Reviewers
* [ ] Iterate until polls are ended and exec doc is confirmed
* [ ] Make sure CI PASS
* [ ] Wait for at Least Two Approvals with local tests to deploy
* [ ] Pre-Deploy Setup and Checks (currently via `dapptools`)
  * [ ] Set local env (`.sethrc`)
    * [ ] Deployer
      * [ ] Avoid using the same deployer for mainnet and testnet (to avoid deploying contracts with the same address but different sources)
      * [ ] `export ETH_PASSWORD=~/.env/password.txt`
      * [ ] `export ETH_KEYSTORE=~/.ethereum/keystore`
      * [ ] `export ETH_FROM=<address>`
    * [ ] Goerli EIP1559
      * [ ] `export ETH_GAS=X`
        * [ ] Run `make estimate` and adjust `ETH_GAS` accordingly (e.g. 6000000)
      * [ ] `export ETH_GAS_PRICE=$(seth --to-wei X gwei)`
        * [ ] Check current gas price using `seth gas-price` and Set `ETH_GAS_PRICE` accordingly (e.g. 40 gwei)
      * [ ] `export ETH_PRIO_FEE=$(seth --to-wei X gwei)`
        * [ ] Check current gas priority fee and set `ETH_PRIO_FEE` accordingly (e.g. 2 gwei)
    * [ ] Goerli RPC URL
      * [ ] `export ETH_RPC_URL=<url>`
    * [ ] Etherscan API KEY
      * [ ] `export ETHERSCAN_API_KEY=<key>`
    * [ ] Ensure to `source` the `.sethrc` file to make env vars available
  * [ ] Check local env
    * [ ] `seth ls`
    * [ ] `seth chain`
* [ ] Deploy spell on Goerli via `make deploy`
  * [ ] Ensure `src/test/config.sol` is edited correctly
    * [ ] `deployed_spell: address(<deployed_spell_address>)`
    * [ ] `deployed_spell_created: <timestamp>`
    * [ ] `deployed_spell_block: <block number>`
    * [ ] validate the above values via `make deploy-info tx=<tx_hash>`
  * [ ] Ensure spell is verified on etherscan
  * [ ] Ensure local tests PASS against deployed spell run via the deploy script
  * [ ] Push auto-generated commit
* [ ] Archive Spell via `make archive-spell` for current date or `date="YYYY-MM-DD" make archive-spell` (date as per cast timestamp)
* [ ] Commit & Push for Review
* [ ] Wait for CI to PASS
* [ ] Wait for at Least Two Approvals
* [ ] Cast Spell via `make cast-spell` (ONLY ON GOERLI)
* [ ] Check `cast()` trace (via [EthTx](https://ethtx.info/))
  * [ ] Ensure no reverts are present that block execution
    * [ ] Inspect low level call reverts if expected
  * [ ] Ensure all actions are executed and not out-of-gas errors are present
* [ ] Pre-Merge Target Branch Pull Attack Checks
  * [ ] Ensure that the latest commit to the spells-mainnet repo was not a maintenance PR
  * [ ] Ensure that there was not a maintenance PR within the last THREE spells (i.e. 6 weeks)
    * [ ] If maintenance PR is present:
      * [ ] PR Name / Description?
      * [ ] PR Actions match description and look safe?
      * [ ] PR Did not modify files unrelated to name / description?
      * [ ] PR Did not modify test script (including CI)?
        * [ ] Run old test script to ensure the test results are the same (use --block flag if spell is already `cast` for Goerli)
        * [ ] Obtain unanimous approval of the safety of the new deploy script
      * [ ] PR Did not modify DssExecLib.address?
        * [ ] If it did, flag with Governance Facilitators
      * [ ] PR Did not affect deploy script?
        * [ ] If it did, flag internally with reviewers and investigate prior to merging
        * [ ] Run old deploy script to ensure the output results are the same
          * [ ] If different, flag with Governance Facilitators (this may affect external scripts or third party reviews)
        * [ ] Obtain unanimous approval of the safety of the new deploy script
* [ ] Squash & Merge
