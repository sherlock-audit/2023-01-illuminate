# Illuminate contest details

- Join [Sherlock Discord](https://discord.gg/MABEWyASkp)
- Submit findings using the issue page in your private contest repo (label issues as med or high)
- [Read for more details](https://docs.sherlock.xyz/audits/watsons)

# Resources

- [Website](https://illuminate-app.vercel.app/lend/)
- [Twitter](https://twitter.com/IlluminateFi)
- [Docs](https://docs.illuminate.finance/)

# On-chain context

TO FILL IN BY PROTOCOL

```
DEPLOYMENT: mainnet
ERC20: USDC and DAI
ERC721: none
ERC777: none
FEE-ON-TRANSFER: none
REBASING TOKENS: none
ADMIN: trusted
```

In case of restricted, by default Sherlock does not consider direct protocol rug pulls as a valid issue unless the protocol clearly describes in detail the conditions for these restrictions. 
For contracts, owners, admins clearly distinguish the ones controlled by protocol vs user controlled. This helps watsons distinguish the risk factor. 
Example: 
* `ContractA.sol` is owned by the protocol. 
* `admin` in `ContractB` is restricted to changing properties in `functionA` and should not be able to liquidate assets or affect user withdrawals in any way. 
* `admin` in `ContractC` is user admin and is restricted to only `functionB`

# Audit scope

~ 2115 nSLOC

- `Lender.sol`
- `MarketPlace.sol`
- `Redeemer.sol`
- `Converter.sol`
- `ERC5095.sol`

Here is a [report](https://docs.google.com/document/d/1M51cKWmZ84BadUMLKGQmCqnTyWRtNvUsk2XJoWRuRHU/edit?usp=sharing) that specifies what has changed since the previous audit.

# About Illuminate

Illuminate is a fixed-rate lending protocol designed to aggregate fixed-yield Principal Tokens and provide Illuminate's users and integrators a guarantee of the best rate in DeFi, while also deepening liquidity across the fixed-rate space.

Most simply described, Illuminate aggregates and wraps principal tokens with similar maturities and underlying assets into one single (meta) principal token (iPTs).

The wrapped / meta principal token (iPT) is traded on a secondary market YieldSpace AMM to provide an on-chain guarantee of the best fixed-yield.

For the best understanding possible, please reference our documentation:

- [Litepaper](https://docs.illuminate.finance/)
- [Smart Contracts Docs](https://docs.illuminate.finance/smart-contracts)
- [Deposit Lifecycle](https://docs.illuminate.finance/smart-contracts/deposit-lifecycle)

# **Important Notes**:

## **Input Sanitization**

When it comes to input sanitization, assuming there are no externalities, we err on the side of having external interfaces validate their input, rather than socializing costs to do checks such as:

- Checking for address(0)
- Checking for input amounts of 0
- Or any similar input sanitization.

## **Admin Privileges**

We strive to ensure users can feel comfortable that there will not be rugs of their funds. We also feel strongly that there also need to be training wheels with new launches, especially when it comes to the integration of numerous external protocols. 

That said, we retain multiple methods for approvals / withdrawals / fees / pausing gated behind admin methods to ensure the protocol can effectively safeguard user funds during the early operation of the protocol. For the most part these methods have delays to give time for users to exit. Further, the admin will always be a multi-sig.

With all this established, we are likely contesting / rejecting most admin centralization issues, unless there are remediations which do not break the ethos of our early / safeguarded launch.

## Areas of Concern

The areas we are most concerned about for this audit are:

- Our Principal Token implementation (`ERC5095.sol`)
- Usage of `holdings` mapping in the Redeemer contract
- Malicious user-provided arguments to `lend` and `redeem` methods
- Any discrepancies between Illuminate PT and external protocol PT accounting

### Out of Scope

- The `mocks` directory

## Building

To build the project, install Foundry.

Then, from the root of the project, run `forge build`.

## Testing

To run the tests, use the following command:

`forge test --fork-url ${RPC_URL} --fork-block-number ${BLOCK_NUMBER} --use solc:0.8.16 --via-ir --no-match-test "Skip\(\B"`

Note that we use fork-mode tests. As a result, you will need to set `RPC_URL` to run those tests.

The provided command will skip tests ending with "Skip". One test in the suite, `testSwivelLend` was skipped due to issues found in Foundry associated with partially verified ERC20 contracts. A unit test for the same method exists and the method has been tested Goerli manually. 
