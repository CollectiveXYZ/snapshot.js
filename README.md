# Snapshot.js

This library forks [snapshot.js](https://github.com/snapshot-labs/snapshot.js) repository, it sends all requests to **[collective-api](https://github.com/snapshot-labs/collective-api)** backend.

### Init client
```typescript
  import snapshot from '@collectiveXYZ/snapshot.js';

  const hub = 'http://localhost:3000'; // collective-api server
  const client = new snapshot.Client712(hub);
  const provider = getDefaultProvider('https://rinkeby.infura.io/v3/<infura_project_id>');
  const wallet = new Wallet('<private_key>', provider);
```


### Create or edit space
```typescript
  const receipt = await client.space(wallet, "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A", {
    space: "test.cartesian.eth",
    from: "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A",
    settings: `{"name":"test space","avatar":"ipfs://QmXpQYCgQ6276RaBAXizkBgCtqabKe8CsXujqhdkStafHQ","symbol":"EQZ","voting":{"type":"single-choice"},"network":"4","plugins":{"safeSnap":{"safes":[{"network":"4","realityAddress":"0xbA7d7EDF58d0dB83e459D585af3330e367a253af"}]}},"categories":[],"strategies":[{"name":"erc721","params":{"symbol":"EQZ","address":"0x01F7FeEB77aE5e04d9606C209a7faFf2187Cd5c1","decimals":18}}],"validation":{"name":"basic","params":{}},"metadata":{"safe":{"address":"0x35452c24a2c288ceff5ae6fa1f68c6c811df1650"}}}`
  })
  console.log(receipt);
```
- `space` must be a subdomain for the root defined in https://github.com/CollectiveXYZ/collective-api/blob/main/.env.dist#L13
- `address` must be the space owner/admin
- `settings`: 
  - `safeSnap` plugin required 
  - `erc721` strategy required
  - `single-choice` voting type 

- **todo:** how to create `collective.xyz` subdomain.

### Create proposal
```typescript
  const receipt = await client.proposal(wallet, "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A", {
    space: 'test.cartesian.eth',
    type: 'single-choice',
    title: 'Test proposal using Collective API',
    body: '',
    choices: ['Yes', 'No'],
    start: 1638799933,
    end: 1639144255,
    snapshot: 13939561,
    network: '4',
    strategies: JSON.stringify([{"name":"erc721","params":{"symbol":"EQZ","address":"0x01F7FeEB77aE5e04d9606C209a7faFf2187Cd5c1","decimals":18}}]),
    plugins: JSON.stringify({"safeSnap":{"safes":[{"network":"4","realityAddress":"0xbA7d7EDF58d0dB83e459D585af3330e367a253af","hash":"0x021022480ce37fbd0656b9124795595ddf58698793d64a2e623779b8b413863a","txs":[{"nonce":0,"hash":"0x842193f5e7428e2aa12028109b042db6c89099f4b9ec43085554beec80e8a07b","transactions":[{"operation":"0","nonce":0,"token":{"name":"Ethereum","decimals":18,"symbol":"ETH","logoUri":"https://safe-transaction-assets.gnosis-safe.io/chains/1/currency_logo.png","address":"main"},"recipient":"0x3fcc740f22A19875647cb7C4A04D4801bB432C1A","type":"transferFunds","data":"0x","to":"0x3fcc740f22A19875647cb7C4A04D4801bB432C1A","amount":"1000000000000000000","value":"1000000000000000000"}]}]}]}}),
    metadata: JSON.stringify({})
  });
  console.log(receipt);
```
- proposal `type` must be `single-choice`
- `strategies` must match `space strategies`

### safeSnap plugin:
Example:
```json
  {
  ...
    "plugins": {
      "safeSnap": {
        "safes": [
          {
            "network": "4",
            "realityAddress": "0xbA7d7EDF58d0dB83e459D585af3330e367a253af",
            "hash": "0x021022480ce37fbd0656b9124795595ddf58698793d64a2e623779b8b413863a", 
            "txs": [
              {
                "nonce": 0,
                "hash": "0x842193f5e7428e2aa12028109b042db6c89099f4b9ec43085554beec80e8a07b",
                "transactions": [
                  {
                    "operation": "0",
                    "nonce": 0,
                    "data": "0x",
                    "to": "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A",
                    "value": "1000000000000000000"
                  }
                ]
              }
            ]
          }
        ]
      }
    },
  ...
  }
```

- Use the safeSnap js plugin to interact with the reality module: https://github.com/snapshot-labs/snapshot-plugins/blob/master/src/plugins/safeSnap/index.ts
- Reality module details: 
  - network 
  - address
  - hash: `keccak256(abi.encodePacked(txs))`
  - txs: array of transactions that will be executed in the Gnosis Safe if the proposal is accepted
    - call `calcTransactionHash` to generate the tx hash
```typescript
    const realityAddress = '0xbA7d7EDF58d0dB83e459D585af3330e367a253af';
    const network = '4';
    const transaction: ModuleTransaction = {
      to: "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A",
      data: "0x",
      nonce: "0",
      value: "1000000000000000000",
      operation: "0"
    };
    const p = new Plugin();
    const txHash = await p.calcTransactionHash(network, realityAddress, transaction);
    console.log(txHash);
```

- Create question in Reality module:
```typescript
await p.submitProposal(web3, moduleAddress, proposalId, [transaction]);
```

- Get reality question details:
```typescript
  const proposalId = "0xa3c5d298e20244fa05e47b46478075de75f9d9dbd464689c72426b6e8819e04b";
  const details = await p.getExecutionDetails(network, moduleAddress, proposalId, [transaction]);
  console.log(details);
```

- Execute proposal in reality module
```typescript
await p.executeProposal(web3, moduleAddress, proposalId, [transaction], transactionIndex);
```

### Vote
```typescript
  const receipt = await client.vote(wallet, "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A", {
    space: 'test.cartesian.eth',
    proposal: '0x1d63cee9c85a944858d021416ccb5685860ab99c4327f402bf4562b4d111f8ab',
    type: 'single-choice',
    choice: 2,
});
```

### Get scores
Calculate voting power for a list of voters.

```typescript
  import snapshot from '@collectiveXYZ/snapshot.js';
  
  const space = 'test.cartesian.eth';
  const strategies = [
    {
      "name": "erc721",
      "params": {
        "symbol": "EQZ",
        "address": "0x01F7FeEB77aE5e04d9606C209a7faFf2187Cd5c1",
        "decimals": 18
      }
    }
  ];
  const network = '4';
  const voters = [
    '0xa478c2975ab1ea89e8196811f51a7b7ade33eb11',
    '0xeF8305E140ac520225DAf050e2f71d5fBcC543e7',
    '0x1E1A51E25f2816335cA436D65e9Af7694BE232ad'
  ];
  const blockNumber = 11437846;
  
  snapshot.utils.getScores(
    space,
    strategies,
    network,
    voters,
    blockNumber
  ).then(scores => {
    console.log('Scores', scores);
  });
```
