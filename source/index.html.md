---
title: NEO DAPI Reference

language_tabs: # must be one of https://git.io/vQNgJ

  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the NEO dAPI. This allows your decentralized application to connect to any NEO wallet which conforms to the dAPI standard. You no longer have to spend any time building out your own wallet, and can instead focus on making your application great. If you'd like to learn more about the use cases of this dAPI, please read below. Otherwise, feel free to skip directly to the method implementation starting with Read Methods.

## DAPI for End Users
As dApps come into the ecosystem, there will be more concerns about the safety of user assets. If dApps all required users to input their private keys in order to use them, it just takes one malicious dApp to steal all their funds. By using a trusted wallet provider which interfaces with the various dApps in the ecosystem on their behalf, users can reduce the exposure of their private keys. This will even allow users to transact with their hardware wallets via the wallet provider, never having to reveal their private keys even to the wallet itself.

## DAPI for dApp Developers
One of the initial hurtles for any developer when starting to develop a dApp is to create a wallet module that will allow the user and application to interface with the NEO blockchain. While there are many quality SDKs out there such as neon-js for facilitating the communication of these requests, there are often many hurtles to successfully constructing the right combination of methods, along with input and output parsing. The issue only gets amplified when trying to integrate with hardware wallet providers such as a Ledger device.

While there may be several options for 3rd party wallet providers that will help them to facilitate these transactions, the is currently no common consensus on the consistency of their interfaces. With a lack of consistency in interfaces to use these wallet provider services, dApp developers will be forced to make a decision to have their platform supported by a single provider, or to double or even triple their development efforts to accommodate all the different wallet provider interfaces. This will lead to a fragmentation in the dApps ecosystem.

## DAPI for Wallet Providers
Each wallet provider, when deciding on supporting dApps to utilize their services as an authentication mechanism will be faced with a decision on how to implement an API to communicate with the dApps. Wallet providers can choose to create their own API from scratch, create their own version of existing projects, or aim to directly duplication an existing API. In the case that the provider decides to make their own API interface from scratch, and try to promote dApps to use it, time and effort will inevitably be wasted by both the provider and competing providers on getting dApp developers on board with using their custom communication interface. If we have a unified interface for such transactions, providers can spend more time on making their individual services better for their users.

# Read Methods

Read methods do not alter the state of the blockchain. It can help you query information about your user, and provide you with relevant information:

## getProvider

```javascript
getProvider()
.then((provider: Provider) => {
  const {
    name,
    website,
    version,
    compatibility,
  } = provider;

  console.log('Provider name: ' + name);
  console.log('Provider website: ' + website);
  console.log('Provider dAPI version: ' + version);
  console.log('Provider dAPI compatibility: ' + JSON.stringify(compatibility));
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_DENIED:
      console.log('The user rejected the request to connect with your dApp.');
      break;
  }
```

> Example Response

```json
{
  "name": "O3",
  "website": "o3.network",
  "version": "1.0",
  "compatibility": ["NEP-1", "NEP-2", "NEP-3"]
}
```

Returns information about the dAPI provider, including who this provider is, the version of their dAPI, and the NEP that the interface is compatible with.

### Provider Response
Parameter | Type |  Description
--------- | ---- |-----------
name | String | The name of the wallet provider
website  | String | The website of the wallet provider
version | String | The version of the dAPI that the the wallet supports
compatibility | [String] | A list of all applicable NEPs which the wallet provider supports

## getNetworks

```javascript
getNetworks()
.then(response => {
  const networks = response.networks;
  console.log('Networks: ' + networks);
  // eg. ["MainNet", "TestNet", "PrivateNet"]
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_DENIED:
      console.log('The user rejected the request to connect with your dApp');
      break;
  }
});
```

> Example Response
```json
{
  "networks": ["MainNet", "TestNet", "PrivateNet"]
}
```

Returns the networks that the wallet provider has available to connect to.

### Networks Response
Parameter | Type |  Description
--------- | ---- |-----------
networks | [String] | A list of all networks which this wallet will allow access to

## getAccount

```javascript
getAccount()
.then((account: Account) => {
  const {
    address,
    publicKey,
  } = account;

  console.log('Provider address: ' + address);
  console.log('Provider public key: ' + publicKey);
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_DENIED:
      console.log('The user rejected the request to connect with your dApp');
      break;
  }
});
```

> Example Response

```json
{
  "address": "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru",
  "publicKey": "03d43468721ef8936548878071b534d8228c313e02e6d90cef8c65fd3c2d4eaeed"
}
```

Return the Account that is currently connected to the dApp.

### Account Response
Parameter | Type |  Description
--------- | ---- |-----------
address | String | The address of the account that is currently connected to the dapp
publicKey | String | The public key of the account that is currently connected to the dapp


## getBalance

```javascript
getBalance({
  params: {
    address: 'AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru',
    assets: ['NKN']
  },
  network: 'MainNet',
})
.then((results: BalanceResults) => {
  Object.keys(results).forEach(address => {
    const { balances } = results[address];
    Object.keys(balances).forEach(balance => {
      const { scriptHash, symbol, amount } = balance

      console.log('Address: ' + address);
      console.log('Asset scriptHash: ' + scriptHash);
      console.log('Asset symbol: ' + symbol);
      console.log('Amount: ' + amount);
    });
  });
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_DENIED:
      console.log('The user rejected the request to connect with your dApp');
      break;
  }
});
```

> Single Address with specific balances requested

```json
//input
{
  "params": {
    "address": "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru",
    "assets": ["NKN"]
  },
  "network": "MainNet",
}

//output
{
  "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru": [
    {
      "scriptHash": "c36aee199dbba6c3f439983657558cfb67629599",
      "symbol": "NKN",
      "amount": "0.00000233",
    }
  ],
}
```

> Single Address with all balances requested

```json
// Query Input
{
  "params": {
    "address": "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru",
  },
  "network": "MainNet",
}

// Query Output
{
  "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru": [
    {
      "scriptHash": "c56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b",
      "symbol": "NEO",
      "amount": "10",
    },
    {
      "scriptHash": "602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7",
      "symbol": "GAS",
      "amount": "777.0001",
    },
    {
      "scriptHash": "c36aee199dbba6c3f439983657558cfb67629599",
      "symbol": "NKN",
      "amount": "0.00000233",
    },
    {
      "scriptHash": "fc732edee1efdf968c23c20a9628eaa5a6ccb934",
      "symbol": "NNC",
      "amount": "2000",
    }
  ]
}
```

> Multiple address balance queries

```json
// Query Input
{
  "params": [
    {
      "address": "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru",
    },
    {
      "address": "AbKNY45nRDy6B65YPVz1B6YXiTnzRqU2uQ",
      "asset": ["PHX"],
    },
  ],
  "network": "MainNet",
}

// Query Output
{
  "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru": [
    {
      "scriptHash": "c56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9",
      "symbol": "NEO",
      "amount": "10",
    },
    {
      "scriptHash": "602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7",
      "symbol": "GAS",
      "amount": "777.0001",
    },
    {
      "scriptHash": "c36aee199dbba6c3f439983657558cfb67629599",
      "symbol": "NKN",
      "amount": "0.00000233",
    },
    {
      "scriptHash": "fc732edee1efdf968c23c20a9628eaa5a6ccb934",
      "symbol": "NNC",
      "amount": "2000",
    }
  ],
  "AbKNY45nRDy6B65YPVz1B6YXiTnzRqU2uQ": [
    {
      "scriptHash": "1578103c13e39df15d0d29826d957e85d770d8c9",
      "symbol": "PHX",
      "amount": "11000",
    }
  ]
}
```


Allows the DAPP to query the balance of a user, this includes both native assets (NEO/GAS) and NEP-5 tokens


### Request Parameters
Parameter | Type |  Description
--------- | ---- |-----------
params |[BalanceRequest] | A list of Balance Request Objects, specifying which addresses, and which assets to query
network | String | The call will only work for the networks available in the GetNetworks command
fetchUTXO? | boolean | The response will fetch NEO and GAS UTXO's if this attribute is true

### Balance Request
Parameter | Type |  Description
--------- | ---- |-----------
address | String   | The address whose balance you want to query
assets  | [String] | A list of symbols to query the balance for

### Response Parameters
Parameter | Type |  Description
--------- | ---- |-----------
address_1 | [BalanceResponse] | This key is the actual address of the query eg. "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru"
address_2 | [BalanceResponse] | This key is the actual address of the query eg. "AbKNY45nRDy6B65YPVz1B6YXiTnzRqU2uQ"
address_n | [BalanceResponse] | This key is the actual address of the query eg. "AUdawyrLMskxXMUE8osX9mSLKz8R7777kE"

<aside class="notice">
The amount of addresses is n where n is the number of addresses specified in your query
</aside>


### BalanceResponse
Parameter | Type |  Description
--------- | ---- |-----------
scriptHash | String | Script hash of the given asset
symbol  | String | Symbol of the given asset
amount | String | Double Value of the balance represented as a String
unspent | [UTXO] | If fetch utxo's was turned on then the utxo array will be returned for the native assets NEO and GAS

### UTXO
Parameter | Type |  Description
--------- | ---- |-----------
asset | String | Script hash of the native asset
createdAtBlock | String | Block number where this utxo was created
index | Int | ???
txid | String | The transaction id of this UTXO
value | String | The double value of this UTXO represented as a String



## getStorage

```javascript
getStorage({
  scriptHash: '505663a29d83663a838eee091249abd167e928f5',
  key: 'game.status',
  network: 'TestNet'
})
.then(res => {
  const value = res.result;
  console.log('Storage value: ' + value);
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_REFUSED:
      console.log('Connection dApp not connected. Please call the "connect" function.');
      break;
    case RPC_ERROR:
      console.log('There was an error when broadcasting this transaction to the network.');
      break;
  }
});
```

> Example Response

```json
{
    "result": "hello world"
}
```


Returns the raw value located in contract storage

### Get Storage Request
Parameter | Type |  Description
--------- | ---- |-----------
scriptHash | String | Scripthash of the contract whose storage you are querying on
key | String | Key of the storage value to retrieve from the contract
network | String? | Network alias to submit this request to. If omitted, will default to "MainNet".

### Get Storage Response 
Parameter | Type |  Description
--------- | ---- |-----------
result  | String | The raw value located in contract storage


## invokeRead

```javascript
invokeRead({
  scriptHash: '505663a29d83663a838eee091249abd167e928f5',
  operation: 'calculatorAdd',
  arguments: [
    {
      type: 'integer',
      value: 2
    },
    {
      type: 'integer',
      value: 10
    }
  ],
  network: 'PrivNet'
})
.then((result: Object) => {
  console.log('Read invocation result: ' + JSON.stringigy(result));
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_REFUSED:
      console.log('Connection dApp not connected. Please call the "connect" function.');
      break;
   case RPC_ERROR:
    console.log('There was an error when broadcasting this transaction to the network.');
    break;
  }
});
```

> Example Response

```json
{
  "script": "8h89fh398f42f.....89hf2894hf9834",
  "state": "HALT, BREAK",
  "gas_consumed": "0.13",
  "stack": [
    {
      "type": "Integer",
      "value": "1337"
    }
  ]
}
```

Execute a contract invocation in read-only mode.

### Invoke Read Request
Parameter | Type |  Description
--------- | ---- |-----------
scriptHash  | String | The script hash of the contract you want to invoke a read on
operation | String | The operation on the smart contract that you want to invoke a read on
args | [Argument] | The input arguments necessary to perform this operation
network | String? | Network alias to submit this request to. If omitted, will default to "MainNet"

### Argument
Parameter | Type |  Description
--------- | ---- |-----------
type | String | The type of the argument with you are using
value | String | String representation of the argument which you are using

### Invoke Read Response
The wallet will return the direct response from the RPC node.

<aside class =notice>
Available types are "String"|"Boolean"|"Hash160"|"Integer"|"ByteArray"|"Array"|"Address"  
</aside>

# Write Methods

Write methods will alter the state on the blockchain on behalf of the user

## send

```javascript
send({
  fromAddress: 'ATaWxfUAiBcQixNPq6TvKHEHPQum9bx79d',
  toAddress: 'ATaWxfUAiBcQixNPq6TvKHEHPQum9bx79d',
  asset: 'GAS',
  amount: '0.0001',
  remark: 'Hash puppy clothing purchase. Invoice#abc123',
  fee: '0.0001',
  network: 'MainNet'
})
.then(({txid, nodeUrl}: SendOutput) => {
  console.log('Send transaction success!');
  console.log('Transaction ID: ' + txid);
  console.log('RPC node URL: ' + nodeUrl);
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_REFUSED:
      console.log('dApp not connected. Please call the "connect" function.');
      break;
    case SEND_ERROR:
      console.log('There was an error when broadcasting this transaction to the network.');
      break;
    case MALFORMED_ADDRESS:
      console.log('The receiver address provided is not valid.');
      break;
    case CANCELED:
      console.log('The user has canceled this transaction.');
      break;
    case INSUFFICIENT_FUNDS:
      console.log('The user has insufficient funds to execute this transaction.');
      break;
  }
});
```

> Example Response

```json
{
  txid: 'ed54fb38dff371be6e3f96e4880405758c07fe6dd1295eb136fe15f311e9ff77',
  nodeUrl: 'http://seed7.ngd.network:10332',
}
```

The send API can be used for accepting payments from the user in a cryptocurrency that is located on the NEO blockchain. It requires user authentication in order for the transaction to be relayed. The transaction will be relayed by the wallet.

### Send Request
Parameter | Type |  Description
--------- | ---- |-----------
fromAddress | String | The address from where the transaction is being sent. This will be the same value as the one received from the getAccount API
toAddress | String | The address to where the user should send their funds
asset | String | The asset which is being requested for payment...e.g GAS or CGAS
amount | String | The amount which is being requested for payment
remark | String? | A transaction attribute remark which may be placed in the transaction, this data will appear in the transaction record on the blockchain
fee | String? | If a fee is specified then the wallet SHOULD NOT override it, if a fee is not specified the wallet SHOULD allow the user to attach an optional fee
network | String? | Network alias to submit this request to. If omitted, will default to "MainNet".

### Send Response 
Parameter | Type |  Description
--------- | ---- |-----------
txid  | String | The transaction id of the send request which can be queried on the blockchain
nodeURL | String | The node to which the transaction was submitted to

<aside class="warning">
It is reccommended that the DAPP take appropriate levels of risk prevention when accepting transactions. The dapp can query the mempool of a known node to ensure that the transaction will indeed be broadcast on the network.
</aside>


## Invoke
```javascript
invoke({
  scriptHash: '505663a29d83663a838eee091249abd167e928f5',
  operation: 'storeData',
  arguments: [
    {
      type: 'string',
      value: 'hello'
    }
  ],
  assets: {
    NEO: '100',
    GAS: '0.0001',
  },
  fee: '0.001',
  network: 'TestNet',
})
.then(({txid, nodeUrl}: InvokeOutput) => {
  console.log('Invoke transaction success!');
  console.log('Transaction ID: ' + txid);
  console.log('RPC node URL: ' + nodeUrl);
})
.catch((err: string) => {
  switch(err) {
    case NO_PROVIDER:
      console.log('No provider available.');
      break;
    case CONNECTION_REFUSED:
      console.log('Connection dApp not connected. Please call the "connect" function.');
      break;
    case RPC_ERROR:
      console.log('There was an error when broadcasting this transaction to the network.');
      break;
    case CANCELED:
      console.log('The user has canceled this transaction.');
      break;
  }
});
```

> Example Response

```json
{
  "txid": "ed54fb38dff371be6e3f96e4880405758c07fe6dd1295eb136fe15f311e9ff77",
  "nodeUrl": "http://seed7.ngd.network:10332"
}
```

Invoke allows for the generic execution of smart contracts on behalf of the user. It is reccommended to have a general understanding of the NEO blockchain, and to be able successfully use all other commands listed previously in this document before attempting a generic contract execution. 

### Invoke Request
Parameter | Type |  Description
--------- | ---- |-----------
scriptHash | String | The script hash of the contract that you wish to invoke
operation | String | The operation on the smart contract that you wish to call. This can be fetched from the contract ABI
args | [Argument] | A list of arguments necessary to perform on the operation you wish to call
fee | String? | If a fee is specified then the wallet SHOULD NOT override it, if a fee is not specified the wallet SHOULD allow the user to attach an optional fee
network | String? | Network alias to submit this request to. If omitted, will default to "MainNet".
assets | AttachedAssets? | Describes the assets to attach with the smart contract, e.g. attaching assets to mint tokens during a token sale
assetIntentOverrides | AssetIntentOverrides | Used to specify the exact the exact UTXO's to use for attached assets. If this is provided fee and attachedAssets will be ignored
triggerContractVerification | Boolean? | Adds the instruction to invoke the contract verifican trigger

### Argument
Parameter | Type |  Description
--------- | ---- |-----------
type | String | The type of the argument with you are using
value | String | String representation of the argument which you are using

<aside class =notice>
Available types are "String"|"Boolean"|"Hash160"|"Integer"|"ByteArray"|"Array"|"Address"  
</aside>

### AttachedAssets
Parameter | Type |  Description
--------- | ---- |-----------
NEO | String? | The amount of NEO to attach to the contract invocation
GAS | String? | The amount of GAS to attach to the contract invocation

### AssetIntentOverrides
Parameter | Type |  Description
--------- | ---- |-----------
inputs | [AssetInput] | A list of UTXO inputs to use for this transaction
outputs | [AssetOutput] | A list of UTXO outputs to use for this transaction

### AssetInput
Parameter | Type |  Description
--------- | ---- |-----------
txid | String | Transaction id to be used as input
index | String | Index of the UTXO, can be found from transaction details

### AssetOutput
Parameter | Type |  Description
--------- | ---- |-----------
asset | String | A list of UTXO inputs to use for this transaction
address | String | A list of UTXO outputs to use for this transaction
value  | String | String representation of double or integer value to be used as output


### Invoke Response 
Parameter | Type |  Description
--------- | ---- |-----------
txid  | String | The transaction id of the send request which can be queried on the blockchain
nodeURL | String | The node to which the transaction was submitted to



# Events
Events are a way for the wallet to asynchronously with the DAPP when certain changes occur to the state of the wallet that might be relevant for the

## READY
On a READY event, the callback will fire with a single argument with information about the wallet provider. At any time a READY event listener is added, it will immidiately be called if the provider is already in a ready state. This provides a single flow for dapp developers since this listener should start any and all interactions with the dapi protocol.
 
Parameter | Type |  Description
--------- | ---- |-----------
name | String | Name of the provider
website | String | Website of the provider
version| String | Version of the dAPI whih the provider supports
compatibility| [String] | List of NEP's which the dAPI provider will support


## ACCOUNT_CHANGED
On a ACCOUNT_CHANGED event, the callback will fire with a single argument of the new account. In the case that the user logs out without providing a new account to connect, this will be null.

Parameter | Type |  Description
--------- | ---- |-----------
address | String | Address of the new account
publicKey | String | Public Key of the new account
