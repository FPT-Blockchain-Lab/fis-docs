# Quorum Permission contracts

## 1. Overview

<image src="../../images/QuorumPermissions.jpg">

## 2. Functions

### 2.1. PermissionsUpgradable

- Store `logical addresses` (guardian, permImpl, permInterface)
- **@notice**: [\_setPolicy()](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/PermissionsUpgradable.sol#L83-L93) function to set the
  permissions policy details in the permissions implementation contract

### 2.2. PermissionsInterface

- Provide `external interface`, internal proxy to logical contract

### 2.3. PermissionsImplementation

- Logic contract, the `actual logic` of the contract is in this contract
- **@notice**: `write methods` only called by `PermissionsInterface` except `setMigrationPolicy` only called by `PermissionsUpgradable`
- **@notice**: [these methods](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/PermissionsImplementation.sol#L120-L207) only are executed
  when [networkBoot = false](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/PermissionsImplementation.sol#L33)
- **@notice**: [addOrg()](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/PermissionsImplementation.sol#L209-L233) only add org (has node
  is running in blockchain network)
- **@notice**: [addSubOrg()](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/PermissionsImplementation.sol#L259-L280) can add subOrg (has
  node is optional)

### 2.4. NodeManager

- This contract holds implementation logic for all `node management functionality`. This can be `called only` by the `implementation contract`
- **@notice**: node statuses:
  > 0 - Not in list 1 - Node pending approval 2 - Active 3 - Deactivated 4 - Blacklisted 5 - Blacklisted node recovery initiated. Once approved the node status
  > will be updated to Active (2)

### 2.5. OrgManager

- This contract holds implementation logic for all `organization management functionality`. This can be `called only` by the `implementation contract`
- **@notice**: org statuses:
  > 0 - Not in list, 1 - Org proposed for approval by network admins 2 - Org in Approved status 3 - Org proposed for suspension and pending approval by network
  > admins 4 - Org in Suspended, once the node is blacklisted no further activity on the node is possible
- **@notice**: [DEPTH_LIMIT & BREATH_LIMIT](https://github.com/ConsenSys/quorum/blob/master/permission/v2/contract/OrgManager.sol#L26-L28) of org is `4`

### 2.6. AccountManager

- This contract holds implementation logic for all `account management functionality`. This can be `called only` by the `implementation contract`
- **@notice**: account statuses:
  > 0 - Not in list 1 - Account pending approval 2 - Active 3 - Inactive 4 - Suspended 5 - Blacklisted 6 - Revoked 7 - Recovery Initiated for blacklisted
  > accounts and pending approval from network admins

### 2.7. RoleManager

- This contract holds implementation logic for all `role management functionality`. This can be `called only` by the `implementation contract`
- **@notice**: role include:
  > 0 - Read only 1 - value transfer 2 - contract deploy 3 - full access 4 - contract call 5 - value transfer and contract call 6 - value transfer and contract
  > deploy 7 - contract call and deploy

### 2.8. VoterManager

- This contract holds implementation logic for all `voter management functionality`. This can be `called only` by the `implementation contract`
- **@notice**: vote types:
  > 0 - None - indicates no pending records for the org 1 - New org add activity 2 - Org suspension activity 3 - Revoke of org suspension 4 - Assigning admin
  > role for a new account 5 - Blacklisted node recovery 6 - Blacklisted account recovery
