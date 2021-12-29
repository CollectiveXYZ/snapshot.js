# Snapshot.js

This library forks [snapshot.js](https://github.com/snapshot-labs/snapshot.js) repository, it sends all requests to **[collective-api](https://github.com/snapshot-labs/collective-api)** backend.

Example:
```typescript
    const hub = 'http://localhost:3000'; // collective-api server
    const client = new snapshot.Client712(hub);
    const provider = getDefaultProvider('https://rinkeby.infura.io/v3/<infura_project_id>');
    const wallet = new Wallet('<private_key>', provider);
    try {
        // create/edit space
        const receipt = await client.space(wallet, "0x30613707713C217BA2A105d2e15BDc7eA2F07Ae6", {
            space: "test.cartesian.eth",
            from: "0x30613707713C217BA2A105d2e15BDc7eA2F07Ae6",
            settings: `{"name":"test_space4","avatar":"ipfs://QmXpQYCgQ6276RaBAXizkBgCtqabKe8CsXujqhdkStafHQ","symbol":"DAIx","voting":{},"network":"4","plugins":{"safeSnap":{"safes":[{"network":"4", "realityAddress": "0xaaabbbb"}]}},"categories":[],"strategies":[{"name":"erc721","params":{"symbol":"DAI","address":"0x6b175474e89094c44da98b954eedeac495271d0f","decimals":18}}],"validation":{"name":"basic","params":{}}}`
        })
      console.log(receipt);

        // create new proposal
        const proposalReceipt = await client.proposal(wallet, "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A", {
          space: 'enghin.cartesian.eth',
          type: 'single-choice',
          title: 'Test proposal using Snapshot.js',
          body: '',
          choices: ['Alice', 'Bob', 'Carol'],
          start: 1638799933,
          end: 1639144255,
          snapshot: 9767718,
          network: '4',
          strategies: JSON.stringify({}),
          plugins: JSON.stringify({}),
          metadata: JSON.stringify({ app: 'snapshot.js' })
        });
        console.log(proposalReceipt);
    } catch(e) {
      console.log("Error", e)
    }
```
