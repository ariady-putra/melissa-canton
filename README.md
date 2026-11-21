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

The following is a step by step illustration.

#### 1. Vault's initial state:

| id | status | issuer | auditor | approvers | threshold | name | assets |
|----|--------|--------|---------|-----------|-----------|------|--------|
| #0:0 | active | 'Issuer' | 'Auditor' | ['Approver1', 'Approver2', 'Approver3'] | 2 | "Vault 101" | Map['Alice'->[Data:Token with name = "COIN"; amount = 100.00]] |

#### 2. Alice (asset owner) creates a TransferProposal (permit):

| id | status | vault | asset.name | asset.amount | from | to | auditor | approvers |
|----|--------|-------|------------|--------------|------|----|---------|-----------|
| #2:0 | active | #0:0 | "COIN" | 20.00 | 'Alice' | 'Bob' | 'Auditor' | [] |

#### 3. Approver1 approves the proposal:

| id | status | vault | asset.name | asset.amount | from | to | auditor | approvers |
|----|--------|-------|------------|--------------|------|----|---------|-----------|
| #3:2 | active | #0:0 | "COIN" | 20.00 | 'Alice' | 'Bob' | 'Auditor' | ['Approver1'] |

#### 4. Approver2 approves the proposal:

| id | status | vault | asset.name | asset.amount | from | to | auditor | approvers |
|----|--------|-------|------------|--------------|------|----|---------|-----------|
| #4:2 | active | #0:0 | "COIN" | 20.00 | 'Alice' | 'Bob' | 'Auditor' | ['Approver2', 'Approver1'] |

#### 5. Vault's final state after Bob (beneficiary) executes Transfer:

| id | status | issuer | auditor | approvers | threshold | name | assets |
|----|--------|--------|---------|-----------|-----------|------|--------|
| #5:3 | active | 'Issuer' | 'Auditor' | ['Approver1', 'Approver2', 'Approver3'] | 2 | "Vault 101" | Map['Alice'->[Data:Token with name = "COIN"; amount = 80.00], 'Bob'->[Data:Token with name = "COIN"; amount = 20.00]] |

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
