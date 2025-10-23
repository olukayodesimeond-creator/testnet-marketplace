Quick summary

Token: TESTX (ERC-20)

Reputation: ACE (non-transferable SBT / on-chain reputation)

Genesis supply: 1,000,000,000 TESTX

Primary uses: rewards, staking, marketplace fees, governance, multipliers for ACE rewards



---

Allocation (exact numbers)

Total = 1,000,000,000 TESTX

1. Community Rewards — 38% = 380,000,000 TESTX


2. Dev & Grants — 22% = 220,000,000 TESTX


3. Team & Early Builders — 12% = 120,000,000 TESTX


4. Treasury / DAO — 15% = 150,000,000 TESTX


5. Partnerships / Integrations — 8% = 80,000,000 TESTX


6. Liquidity / Market Ops — 5% = 50,000,000 TESTX



Total check: 380,000,000 + 220,000,000 + 120,000,000 + 150,000,000 + 80,000,000 + 50,000,000 = 1,000,000,000.


---

Unlock / Vesting rules (practical defaults)

Team: 12 month cliff, then 36 month linear vest.

Dev & Grants: 6 month cliff, then 24 month linear distribution.

Community Rewards: allocated to on-chain reward pool; distributed programmatically per epoch (see emission).

Treasury: locked governance control, can be used by DAO proposals.

Partnerships / Liquidity: allocated on proposals or executed via multi-sig.



---

Emission schedule & reward pool mechanics

Design goal: strong early incentives for testnet activity, then taper.

Initial circulating at TGE: 10% of total supply = 100,000,000 TESTX (covers liquidity, initial partnerships, ops).

Reward pool: majority of Community Rewards (380M) lives in a contract and is distributed over 5 years with front loaded year 1. Example schedule: Year1 = 30% of 380M = 114M, Year2 = 25% = 95M, Year3 = 20% = 76M, Year4 = 15% = 57M, Year5 = 10% = 38M. That sums to 380M.

Inflation control: additional minting disabled by default. Any future changes require DAO vote. If you want controlled inflation, put a DAO adjustable inflation parameter with a hard cap and timelock governance.



---

ACE mechanics (non-transferable reputation)

ACE is the on-chain metric described in your docs. Implementation notes and formulas follow.

Storage model

Implement ACE as a soulbound token or on-chain mapping: mapping(address => uint256) public ACE;

ACE updates are triggered by RewardManager after verifying proofs or off-chain indexer validation. ACE is non-transferable and bound to a wallet or SBT identity.


Earning rules (examples)

Swap volume: ACE += floor( volume_in_TESTX / 10 )

Example: swap 1,000 TESTX -> floor(1000 / 10) = 100 ACE.


NFT burn (for rewards): ACE += floor( burn_weight * rarity_multiplier ) where burn_weight is defined per collection.

Prediction accuracy: ACE += floor(accuracy_score * 10) (where accuracy_score is percent e.g. 0.8 -> 8 ACE).

Research submission: base ACE += 50 then originality AI + community upvotes multiply it (see multipliers).

LP staking time: ACE += floor( staked_TESTX / 1000 * days_staked / 30 ) — gives time-weighted ACE.


Decay

Passive decay to keep rankings fresh. Example: 1.5% decay per 30 days. Implement as epoched decay: on updateACE(user) apply ACE = ACE * 0.985^(months_inactive).


Multipliers & tiers

ACE defines tiers which affect $TESTX earning rate and privileges.


Example tier mapping:

Bronze: ACE 0–499 → multiplier 1x

Silver: ACE 500–1999 → multiplier 1.5x

Gold: ACE 2,000–9,999 → multiplier 2.5x

Platinum: ACE 10,000+ → multiplier 4x


Tier multipliers stack with staking multiplier (see staking).


---

Reward formula (concrete)

When a user performs a rewardable action:

Let:

base_reward = TESTX amount assigned to that action (protocol config)

ACE_mult = 1 + (tier_multiplier - 1) derived from ACE tier

stake_mult = 1 + 0.5 * ln(1 + staked_TESTX / 1,000) capped at +2.0 (cap means max stake_mult = 3x)

privacy_bonus = 0.0 to 0.5 depending on FHE usage


Then:

user_reward = base_reward * ACE_mult * stake_mult * (1 + privacy_bonus)

Concrete example:

base_reward = 10 TESTX

user ACE = 2,500 → Gold → ACE_mult = 2.5

user stakes 5,000 TESTX → stake_mult = 1 + 0.5 * ln(1 + 5000/1000) ≈ 1 + 0.5ln(6) ≈ 1 + 0.51.7918 ≈ 1.8959 (~1.8959)

privacy_bonus = 0.2


Compute step by step:

1. ACE_mult = 2.5


2. stake_mult ≈ 1.8959


3. privacy factor = 1 + 0.2 = 1.2


4. user_reward = 10 * 2.5 * 1.8959 * 1.2

10 * 2.5 = 25

25 * 1.8959 ≈ 47.3975

47.3975 * 1.2 ≈ 56.877 TESTX




So a 10 TESTX base action becomes ~56.88 TESTX because of ACE + staking + privacy incentives.


---

Staking & Lock mechanics

Stake contract: stake TESTX to boost reward multiplier and governance weight.

Lock options: flexible staking (no lock) yields small multiplier; 30/90/365 day locks give step increases. Example multipliers: no lock = base, 30d = +10%, 90d = +30%, 365d = +100% on stake_mult contribution. Combine with logarithmic stake formula above and cap total stake_mult at 3x.



---

Fees & revenue split

Swap / market fees settable by DAO. Example default: 0.3% per swap.

Split:

LPs: 50% of fees

Reward Pool: 30% of fees (adds to Community Rewards for ACE payouts)

Treasury: 20% (DAO controlled for partnerships, ops)



---

Governance

1 TESTX = 1 vote for protocol-level proposals.

ACE does not grant governance votes but can define proposal eligibility, e.g. proposals require proposer to have ACE > X or stake Y TESTX. That prevents low-quality spam proposals.

Progressive decentralization: multisig to DAO treasury transfer once community distribution reaches target.



---

On-chain contract mapping (recommended)

TESTX — ERC20 token contract (standard, permit optional).

ACERegistry — SBT or mapping contract storing ACE values and metadata. Methods: increaseACE, decayACE, getTier. Only RewardManager and Verifier allowed to call increaseACE.

RewardManager — core reward logic; pulls from Community Reward pool; handles epoch distribution and interacts with FHE verifier for private proofs.

StakingPool — stake TESTX, manage locks and multipliers.

FeeSplitter — handles fee distribution.

Governance — timelock + voting module.

FHEVerifier — on-chain verifier stub or oracle that accepts zero knowledge / FHE proofs for private actions. For heavy FHE verification, do off-chain verification then submit succinct proof / attestation on-chain.



---

FHE privacy integration (practical)

Keep token flows transparent on chain, but use FHE for sensitive scoring and verification: research originality check, biometric attestations, and exact private volume for privacy bonus. Implementation pattern:

1. Off-chain FHE computation validates private input.


2. Off-chain attestor signs a succinct proof or attestation.


3. User submits the attestation to RewardManager.


4. RewardManager verifies attestation and mints/disburses reward.



Optionally store encrypted state in calldata only readable by FHE-enabled contracts or host nodes. Avoid putting raw biometric or personal data on chain.



---

Security / anti-abuse guardrails

ACE anti-Sybil: require KYC-like attestations via zkID, Worldcoin, or cross-chain reputation as condition for big ACE awards.

Oracle / Attestor slashing: attestors that sign bad proofs can be slashed from a staked bond.

Rate limits: per-wallet caps on ACE gains per epoch to prevent farming.

Challenge windows: allow community disputes on awarded ACE for a fixed window.



---

Initial parameters (put into config file / contract constants)

Total supply = 1,000,000,000 (1e9) TESTX

Initial circulating = 100,000,000 (10%)

Community pool = 380,000,000 (distributed over 5 years)

Swap fee default = 0.3%

ACE decay = 1.5% per 30 days

ACE swap conversion = 1 ACE per 10 TESTX swapped (configurable)

Staking multiplier cap = 3x
