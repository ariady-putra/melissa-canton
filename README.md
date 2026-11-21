# Melissa

Melissa is a multi-sig contract (vault) where each action requires approval determined by the vault's threshold value.

This is a personal open-ended project to explore various Canton concepts.

Each vault is singleton (1 UTxO per vault).

The proposal templates are _permit_-like, it is designed such that the underlying vault remains active when there are pending proposals.
When there are competing proposals, the first approved proposal to be executed wins, and the remaining pending proposals become automatically invalid (even when it passes the approval threshold). The proposer of each proposal can cancel stale proposals.

## Actions

Currently, there are 4 actions:
- Transfer
- AddApprover
- RemoveApprover
- SetThreshold

### Transfer

Each vault can store assets held by multiple owners (EVM-style):
```haskell
assets : Map Party [Token]
```

To perform a transfer action:
1. The sender creates a TransferProposal (permit)
2. The approvers approve the proposal
3. The receiver can perform the Transfer action

### AddApprover

To add an approver:
1. A vault stakeholder creates a proposal (AddApproverProposal)
2. The approvers approve the proposal
3. The new approver can perform the AddApprover action

### RemoveApprover

To remove an approver:
1. A vault stakeholder creates a proposal (RemoveApproverProposal)
2. The approvers approve the proposal
3. The proposer can perform the RemoveApprover action

### SetThreshold

To update the threshold value:
1. A vault stakeholder creates a proposal (SetThresholdProposal)
2. The approvers approve the proposal
3. The proposer can perform the SetThreshold action
