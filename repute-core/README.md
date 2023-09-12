# Repute-core

This module serves as a POC for the modular design for a reputation management system. In the long run, RMS is meant to be pluggable (but engine-wise and incentive-wise) and follow certain standards. But this POC represents a compacted design for RMS.

Incentive system: ranks, repute-power

Engine: Social Networks

Check here to learn more about what these too mean.

With Repute-core, an address can start getting scores by priming. This gives it an initial score of zero. The engine calculates scores based on backings and oppositions from other primed addresses. only post-primed address can back or oppose other accounts. the weight of backings and oppositions depend on multiple criteria, including the voter's current score, % of successfule oppossitions/backings, rank, etc

## Data structures

### General information

```rust
pub struct UserInfo<AccountId, ReputeRank, PrimeStatus> {
    Pub identity: AccountIdentity,
    Pub user_id: UserId,
    Pub Account_status: PrimeStatus,
    Pub total_backers: TotalBackers,
    Pub total_scores_gained: TotalScoresGained,
    Pub total_opponents: TotalOponents,
    Pub total_scores_lost: TotalScoresLost,
    Pub reput_power: ReputePower,
    Pub repute_rank: ReputeRank,
}
```

### Reputation rank of Account

```rust
pub enum rank {
    Primed,
    NoviceRep,
    ReputationBuilder,
    TrustworthyMember,
    ReputationEvaluator,
    EsteemedContributor,
    ReputationGuardian,
    ReputationGuru,
    ReputationMaster,
    DistinguishedReputician,
    ReputationLegend,
    ReputationIcon,
    SupremeReputation,
}
```

### Status of Account

```rust
pub enum PrimeStatus {
    Primed, // for accounts that have been primed but have a score of `zero`
    PostPrimed, // for accounts with scores > `zero`
}
```

## Storage Items
- `TotalPrimed` : Total number of account that have at least the `primed` rank (the `primed` rank is the lowest of all ranks)
- `rankOf` : Rank of an account
- `TotalScoreLostOf` : Total score an account has lost since priming
- `TotalScoreGainedOf` : Total score an account has gained since priming
- `CurrentScoreOf` : Current score of an account
- `TotalOpponentsOf` : Total Opponents of an account
- `TotalBackersOF` : Total backers of an account
- `ReputePowerOf` :  power of voter in governance and effect of voters power on changes to other people’s reputation. This is calculated from rank, current score, total opponents, total backers, number of oppositions, number of backings,  percentage of successful oppositions, percentage of successful backings etc
- `StatusOf` : This can be `Primed` or `PostPrimed`

## Calls
- `Prime` : This gives an account a score of `zero`
- `Back` : Used by an account of the `post-primed` status to increase the score of another account
- `Oppose` : Used by an account of the `post-primed` status to decrease the score of another account
- `Rescore` : Used to request that an account be rescored. This will require the lock-up of some reputation parameters, which are destroyed if the attempt to rescore fails


####  To reduce the chances of puppet accounts, The following actions are kept in place: 

- Only accounts above the `Primed` rank can Back, Oppose or Rescore.
- Attempting to rescore will require the lock up of a significant amount of scores, which means only accounts with high reputation can attempt to rescore. If this attempt fails, the locked-up score is slashed
- It'll take a significant amount of efforts to move from the `Primed` rank to higher ranks, even if an accounts has enough scores to attain these ranks. This is in order to avoid spam. `Primed` accounts will need to fulfil other criteria like age of account. 
