1. **Prerequisites**:

- A private network if deploying a public contract.
- A privacy-enabled network if deploying a private contract (public contracts can also be deployed on privacy-enabled networks).

2. **Create** a smart contract and **compile** code to get smart contract's outputs JSON.
3. [**Deploy contract docs of GoQuorum**](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/)
   3.1 **Public contract**

- [Using `eth_sendTransaction`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#1-using-eth_sendtransaction) with parameters:
  - `from` - Address of the sender’s account.
  - `to` - Address of the receiver. To deploy a contract, set to `null`.
  - `gasPrice` - Price for each unit of gas the sender is willing to pay. [Set to zero](https://consensys.net/docs/goquorum/en/latest/concepts/free-gas-network/) in GoQuorum networks.
  - `data` - One of the following:. - For contract deployments (this use case), the [compiled binary of the contract](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#compile-the-contract). - For contract interactions, the hash of the invoked method signature and encoded parameters (see [Ethereum Contract ABI](https://solidity.readthedocs.io/en/develop/abi-spec.html)). - For simple ether transfers, empty.
    > curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendTransaction","params":
    > [{"from":"0xf0e2db6c8dc6c681bb5d6ad121a107f300e9b2b5", "to":null,
    >
    > > "gas":"0x24A22","gasPrice":"0x0",
    > > "data":"0x608060405234801561001057600080fd5b5060405161014d38038061014d8339818101604052602081101561003357600080fd5b8101908080519060200190929190505050806000819055505060f38061005a6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80632a1afcd914604157806360fe47b114605d5780636d4ce63c146088575b600080fd5b604760a4565b6040518082815260200191505060405180910390f35b608660048036036020811015607157600080fd5b810190808035906020019092919050505060aa565b005b608e60b4565b6040518082815260200191505060405180910390f35b60005481565b8060008190555050565b6000805490509056fea2646970667358221220e6966e446bd0af8e6af40eb0d8f323dd02f771ba1f11ae05c65d1624ffb3c58264736f6c63430007060033"}], "id":1}' -H 'Content-Type: application/json' http://localhost:8545
- [Using `web3.eth.Contract`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#2-using-web3ethcontract):

  > const path = require('path');
  > const fs = require('fs-extra');
  > const web3 = new Web3(host);
  > // use the existing Member1 account address or make a new account
  > const address = "f0e2db6c8dc6c681bb5d6ad121a107f300e9b2b5";
  > // read in the contracts
  > const contractJsonPath = path.resolve(\_\_dirname, 'SimpleStorage.json');
  > const contractJson = JSON.parse(fs.readFileSync(contractJsonPath));
  > const contractAbi = contractJson.abi;
  > const contractByteCode = contractJson.evm.bytecode.object
  > async function createContract(host, contractAbi, contractByteCode, contractInit, fromAddress) {

       const web3 = new Web3(host)
       const contractInstance = new web3.eth.Contract(contractAbi);
       const ci = await contractInstance
         .deploy({ data: '0x'+contractByteCode, arguments: [contractInit] })
         .send({ from: fromAddress, gasLimit: "0x24A22" })
         .on('transactionHash', function(hash){
           console.log("The transaction hash is: " + hash);
         });
       return ci;

  };
  // create the contract
  async function main(){
  // using Member1 to send the transaction from
  createContract("http://localhost:20000", contractAbi, contractByteCode, 47, address)
  .then(async function(ci){
  console.log("Address of transaction: ", ci.options.address);
  })
  .catch(console.error);
  }

- [Using `eth_sendSignedTransaction`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#3-using-eth_sendsignedtransaction)

  > const web3 = new Web3(host);
  > // use an existing account, or make an account
  > const privateKey = "0x8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63";
  > const account = web3.eth.accounts.privateKeyToAccount(privateKey);
  > // read in the contracts
  > const contractJsonPath = path.resolve(**dirname, 'SimpleStorage.json');
  > const contractJson = JSON.parse(fs.readFileSync(contractJsonPath));
  > const contractAbi = contractJson.abi;
  > const contractBinPath = path.resolve(**dirname,'SimpleStorage.bin');
  > const contractBin = fs.readFileSync(contractBinPath);
  > // initialize the default constructor with a value `47 = 0x2F`; this value is appended to the bytecode
  > const contractConstructorInit =
  > "000000000000000000000000000000000000000000000000000000000000002F";
  > // get txnCount for the nonce value
  > const txnCount = await web3.eth.getTransactionCount(account.address);
  > const rawTxOptions = {

      nonce: web3.utils.numberToHex(txnCount),
      from: account.address,
      to: null, //public tx
      value: "0x00",
      data: '0x'+contractBin+contractInit, // contract binary appended with initialization value
      gasPrice: "0x0", //ETH per unit of gas
      gasLimit: "0x24A22" //max number of gas units the tx is allowed to use

  };
  console.log("Creating transaction...");
  const tx = new Tx(rawTxOptions);
  console.log("Signing transaction...");
  tx.sign(privateKey);
  console.log("Sending transaction...");
  var serializedTx = tx.serialize();
  const pTx = await web3.eth.sendSignedTransaction('0x' + serializedTx.toString('hex').toString("hex"));

  3.2 **Private contract**

- [Using Using `eth_sendTransaction`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#1-using-eth_sendtransaction_1) with the same parameters in 3.1 and add some parameters
  - `privateFrom` - The sender’s base-64-encoded public key.
  - `privateFor` - Array of the recipient’s base-64-encoded public keys.
  - `privacyFlag` - 0 for standard private, 1 for [counter party protection](https://consensys.net/docs/goquorum/en/latest/concepts/privacy/privacy-enhancements/#counter-party-protection). and 3 for [private state validation](https://consensys.net/docs/goquorum/en/latest/concepts/privacy/privacy-enhancements/#private-state-validation).
    > curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xf0e2db6c8dc6c681bb5d6ad121a107f300e9b2b5", "to":null, "gas":"0x24A22","gasPrice":"0x0", "privateFrom": "BULeR8JyUWhiuuCMU/HLA0Q5pzkYT+cHII3ZKBey3Bo=", "privateFor": ["1iTZde/ndBHvzhcl7V68x44Vx7pl8nwx9LqnM/AfJUg="], "privacyFlag": 0,"data":"0x608060405234801561001057600080fd5b5060405161014d38038061014d8339818101604052602081101561003357600080fd5b8101908080519060200190929190505050806000819055505060f38061005a6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80632a1afcd914604157806360fe47b114605d5780636d4ce63c146088575b600080fd5b604760a4565b6040518082815260200191505060405180910390f35b608660048036036020811015607157600080fd5b810190808035906020019092919050505060aa565b005b608e60b4565b6040518082815260200191505060405180910390f35b60005481565b8060008190555050565b6000805490509056fea2646970667358221220e6966e446bd0af8e6af40eb0d8f323dd02f771ba1f11ae05c65d1624ffb3c58264736f6c63430007060033"}], "id":1}' -H 'Content-Type: application/json' http://localhost:20000
- [Using `web3.eth.Contract`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#2-using-web3ethcontract_1)
  > const path = require('path');
  > const fs = require('fs-extra');
  > const web3 = new Web3(host);
  > // use the existing Member1 account or make a new account
  > const privateKey = "0x8f2a55949038a9610f50fb23b5883af3b4ecb3c3bb792cbcefbd1542c692be63";
  > // read in the contracts
  > const contractJsonPath = path.resolve(\_\_dirname, 'SimpleStorage.json');
  > const contractJson = JSON.parse(fs.readFileSync(contractJsonPath));
  > const contractAbi = contractJson.abi;
  > const contractByteCode = contractJson.evm.bytecode.object
  > async function createContract(host, contractAbi, contractByteCode, contractInit, fromAddress,
  > toPublicKey) {
       const web3 = new Web3(host)
       const contractInstance = new web3.eth.Contract(contractAbi);
       const ci = await contractInstance
         .deploy({ data: '0x'+contractByteCode, arguments: [contractInit] })
         .send({ from: fromAddress, privateFor: [toPublicKey], gasLimit: "0x24A22" })
         .on('transactionHash', function(hash){
           console.log("The transaction hash is: " + hash);
         });
       return ci;
  };
  // create the contract
  async function main(){
  // sending the transaction from Member1 to Member3
  createContract("http://localhost:20000", contractAbi, contractByteCode, 47,
  "f0e2db6c8dc6c681bb5d6ad121a107f300e9b2b5",
  "1iTZde/ndBHvzhcl7V68x44Vx7pl8nwx9LqnM/AfJUg=")
  .then(async function(ci){
  console.log("Address of transaction: ", ci.options.address);
  })
  .catch(console.error);
  }
- [Using `priv.generateAndSendRawTransaction`](https://consensys.net/docs/goquorum/en/latest/tutorials/contracts/deploying-contracts/#3-using-privgenerateandsendrawtransaction)
  > const Web3 = require('web3');
  > const Web3Quorum = require('web3js-quorum');
  > // read in the contracts
  > const contractJsonPath = path.resolve(**dirname, 'SimpleStorage.json');
  > const contractJson = JSON.parse(fs.readFileSync(contractJsonPath));
  > const contractAbi = contractJson.abi;
  > const contractBinPath = path.resolve(**dirname,'SimpleStorage.bin');
  > const contractBin = fs.readFileSync(contractBinPath);
  > // initialize the default constructor with a value `47 = 0x2F`; this value is appended to the bytecode
  > const contractConstructorInit =
      "000000000000000000000000000000000000000000000000000000000000002F";
  // account details
  // unlock the account so you can sign the tx
  const accountKeyPath =
  path.resolve(\_\_dirname,'../../','config/quorum/networkFiles/member1/accountkey');
  const accountKey = JSON.parse(fs.readFileSync(accountKeyPath));
  const signingAccount = web3.eth.accounts.decrypt(accountKey, "");
  const web3 = new Web3("http://localhost:20000");
  const web3quorum = new Web3Quorum(web3, {privateUrl: client.privateUrl}, true);
  // get the nonce for the accountAddress
  const accountAddress = client.accountAddress;
  const txCount = await web3.eth.getTransactionCount(`0x${accountAddress}`);
  const txOptions = {
  nonce: txCount,
  gasPrice: 0, //Set to 0 in GoQuorum networks
  gasLimit: 0x24A22, //max number of gas units the tx is allowed to use
  value: 0,
  data: '0x'+contractBin+contractConstructorInit,
  from: signingAccount,
  isPrivate: true,
  privateKey: fromPrivateKey,
  privateFrom: fromPublicKey,
  privateFor: [toPublicKey]
  };
  console.log("Creating contract...");
  const txHash = await web3quorum.priv.generateAndSendRawTransaction(txOptions);
  console.log("Getting contractAddress from txHash: ", txHash);
  console.log("Private Transaction Receipt: ", txHash.contractAddress);

4. [**Deploy contract docs of Hyperledger Besu**](https://besu.hyperledger.org/en/stable/Tutorials/Contracts/Deploying-Contracts/)

- Besu doesn't support `eth_sendTransaction` so we use the [`eea_sendRawTransaction`](https://besu.hyperledger.org/en/latest/Reference/API-Methods/#eea_sendrawtransaction) to keep account management separate for stronger security. Configure [`EthSigner`](https://docs.ethsigner.consensys.net/en/stable/) with your Besu node to make the `eth_sendTransaction` API call.
  4.1 **Public contract**
- Like the deployment of public contract in `3.1`
  4.2 **Private contract**
- Using `eea_sendRawTransaction for private contracts with web3js-quorum`
  > const Web3 = require('web3');
  > const Web3Quorum = require('web3js-quorum');
  > const bytecode =
  > "608060405234801561001057600080fd5b5060405161014d38038061014d8339818101604052602081101561003357600080fd5b8101908080519060200190929190505050806000819055505060f38061005a6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80632a1afcd914604157806360fe47b114605d5780636d4ce63c146088575b600080fd5b604760a4565b6040518082815260200191505060405180910390f35b608660048036036020811015607157600080fd5b810190808035906020019092919050505060aa565b005b608e60b4565b6040518082815260200191505060405180910390f35b60005481565b8060008190555050565b6000805490509056fea2646970667358221220e6966e446bd0af8e6af40eb0d8f323dd02f771ba1f11ae05c65d1624ffb3c58264736f6c63430007060033";
  > // initialize the default constructor with a value `47 = 0x2F`; this value is appended to the bytecode
  > const contractConstructorInit =
      "000000000000000000000000000000000000000000000000000000000000002F";
  const chainId = 1337;
  const web3 = new Web3(clientUrl);
  const web3quorum = new Web3Quorum(web3, chainId);
  const txOptions = {
  data: '0x'+bytecode+contractConstructorInit,
  privateKey: fromPrivateKey,
  privateFrom: fromPublicKey,
  privateFor: [toPublicKey]
  };
  console.log("Creating contract...");
  const txHash = await web3quorum.priv.generateAndSendRawTransaction(txOptions);
  console.log("Getting contractAddress from txHash: ", txHash);
  const privateTxReceipt = await web3quorum.priv.waitForTransactionReceipt(txHash);
  console.log("Private Transaction Receipt: ", privateTxReceipt);
  return privateTxReceipt;
- Using `eea_sendRawTransaction for private contracts with web3js-eea` (This `web3js-eea` library will be `deprecated` on `December 31, 2021`. Please use the `web3js-quorum` library instead and refer to the previous section)
  > const Web3 = require('web3');
  > const EEAClient = require('web3-eea');
  > const bytecode =
  > "608060405234801561001057600080fd5b5060405161014d38038061014d8339818101604052602081101561003357600080fd5b8101908080519060200190929190505050806000819055505060f38061005a6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80632a1afcd914604157806360fe47b114605d5780636d4ce63c146088575b600080fd5b604760a4565b6040518082815260200191505060405180910390f35b608660048036036020811015607157600080fd5b810190808035906020019092919050505060aa565b005b608e60b4565b6040518082815260200191505060405180910390f35b60005481565b8060008190555050565b6000805490509056fea2646970667358221220e6966e446bd0af8e6af40eb0d8f323dd02f771ba1f11ae05c65d1624ffb3c58264736f6c63430007060033";
  > // initialize the default constructor with a value `47 = 0x2F`; this value is appended to the bytecode
  > const contractConstructorInit =
      "000000000000000000000000000000000000000000000000000000000000002F";
  const web3 = new Web3(clientUrl);
  const chainId = 1337;
  const web3eea = new EEAClient(web3, chainId);
  const txOptions = {
  data: '0x'+bytecode+contractConstructorInit,
  privateKey: fromPrivateKey,
  privateFrom: fromPublicKey,
  privateFor: [toPublicKey]
  };
  console.log("Creating contract...");
  const txHash = await web3eea.eea.sendRawTransaction(txOptions);
  console.log("Getting contractAddress from txHash: ", txHash);
  const privateTxReceipt = await web3.priv.getTransactionReceipt(txHash, fromPublicKey);
  // console.log("Private Transaction Receipt: ", privateTxReceipt);
  return privateTxReceipt;
