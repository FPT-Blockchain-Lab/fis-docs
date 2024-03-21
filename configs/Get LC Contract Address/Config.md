**Contract Config:**

Access to repo [https://github.com/FPT-Blockchain-Lab/SMC-LC](https://github.com/FPT-Blockchain-Lab/SMC-LC) of FPT-Blockchain-Lab. How to make repo works?

- Change config in env file

```jsx
# mnemonic for test on development network (hardhat)
DEVELOPMENT_ACCOUNT_MNEMONIC_0="test test test test test test test test test test test junk"

# Address of PermissionInterface contract (Quorum)
PERMISSION_INTERFACE="0x6c7898a23FEd000263d22AAdA36b56F207e2bA5b"
// address of permission interface -> get in permission contract config

# MANAGEMENT_ORG name (It could be keccak256(MANAGEMENT_ORG).toString())
MANAGEMENT_ORG="FIS"
// parent org

# Blockchain network provider URI
RINKEBY_PROVIDER="https://eth-rinkeby.alchemyapi.io/v2/K2pWuRnqqrdvJ3DmSCTA-6PQBFl30Wjs"

# FPT testnet provider
FPT_TEST_PROVIDER="http://10.14.104.6:32443"
// Node FIS public
FPT_TEST_CHAIN_ID=6789
// Chain id of network
# Sing space delimeter string
FPT_TEST_ACCOUNT_PRIVATE_KEYS="PRIVATE_KEY_1 PRIVATE_KEY_2"
// Private key of account you wants to use as network admin
```

- Change contracts code when something change.
- Run commmand: `npx hardhat run --network fpt-test scripts/full_deployment.js` to get all contract addresses.
