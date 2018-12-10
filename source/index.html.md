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

Welcome to the NEO dAPI. This allows your decentralized application to connect to any NEO wallet which conforms to the dAPI standard. You no longer have to spend any time building out your own wallet, and can instead focus on making your application great.

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

> The compatibility field will return a list of strings that corresponds to NEPs that the dAPI conforms to

```json
{
  "name": "O3",
  "website": "o3.network",
  "version": "1.0",
  "compatibility": ["NEP-1", "NEP-2", "NEP-3"]
}
```


Returns information about the dAPI provider, including who this provider is, the version of their dAPI, and the NEP that the interface is compatible with.

## getNetworks

Returns the networks that the wallet provider has available to connect to.

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


```json
{
  "networks": ["MainNet", "TestNet", "PrivateNet"]
}
```

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


```json
{
  address: "AeysVbKWiLSuSDhg7DTzUdDyYYKfgjojru",
  publicKey: "03d43468721ef8936548878071b534d8228c313e02e6d90cef8c65fd3c2d4eaeed"
}
```

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

Single Address with specific balances requested

```json
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

Single Address with all balances requested

```json
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

Multiple address balance queries

```json
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

## getStorage

Returns the raw value located in contract storage

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


```json
{
    "result": "hello world"
}
```

## invokeRead

Execute a contract invocation in read-only mode.
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

```json
{
  script: '8h89fh398f42f.....89hf2894hf9834',
  state: 'HALT, BREAK',
  gas_consumed: '0.13',
  stack: [
    {
      type: 'Integer',
      value: '1337'
    }
  ]
}
```

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

```json
{
  txid: 'ed54fb38dff371be6e3f96e4880405758c07fe6dd1295eb136fe15f311e9ff77',
  nodeUrl: 'http://seed7.ngd.network:10332',
}
```

## invoke
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

```json
{
  txid: 'ed54fb38dff371be6e3f96e4880405758c07fe6dd1295eb136fe15f311e9ff77',
  nodeUrl: 'http://seed7.ngd.network:10332',
}
```


# Events

## Event Changed


