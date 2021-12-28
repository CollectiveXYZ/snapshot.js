# Snapshot.js

This library forks [snapshot.js](https://github.com/snapshot-labs/snapshot.js) repository, it sends all requests to **[collective-api](https://github.com/snapshot-labs/collective-api)** backend.

Example:
```typescript
    const hub = 'http://localhost:3000'; // collective-api server
    const client = new snapshot.Client712(hub);
    const provider = getDefaultProvider('https://rinkeby.infura.io/v3/<infura_project_id>');
    const wallet = new Wallet('<private_key>', provider);
    try {
        // @ts-ignore
        const receipt = await client.space(wallet, "0x30613707713C217BA2A105d2e15BDc7eA2F07Ae6", {
            space: "test.cartesian.eth",
            from: "0x30613707713C217BA2A105d2e15BDc7eA2F07Ae6",
            settings: `{"name":"test_space4","avatar":"ipfs://QmXpQYCgQ6276RaBAXizkBgCtqabKe8CsXujqhdkStafHQ","symbol":"DAIx","voting":{},"network":"4","plugins":{"safeSnap":{"safes":[{"network":"4", "realityAddress": "0xaaabbbb"}]}},"categories":[],"strategies":[{"name":"erc721","params":{"symbol":"DAI","address":"0x6b175474e89094c44da98b954eedeac495271d0f","decimals":18}}],"validation":{"name":"basic","params":{}}}`
        })
      console.log(receipt);
    }catch(e) {
      console.log("Error", e)
    }
```
