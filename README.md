# Snapshot.js

This library forks [snapshot.js](https://github.com/snapshot-labs/snapshot.js) repository, it sends all requests to **[collective-api](https://github.com/snapshot-labs/collective-api)** backend.

### Init client
```typescript
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
    settings: `{"name":"test space","avatar":"ipfs://QmXpQYCgQ6276RaBAXizkBgCtqabKe8CsXujqhdkStafHQ","symbol":"EQZ","voting":{"type": "single-choice"},"network":"1","plugins":{"safeSnap":{"safes":[{"network":"1","realityAddress":"0xaaabbbb"}]}},"categories":[],"strategies":[{"name":"erc721","params":{"symbol":"EQZ","address":"0x1Da87b114f35E1DC91F72bF57fc07A768Ad40Bb0","decimals":18}}],"validation":{"name":"basic","params":{}}}`
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
    title: 'Test proposal using Collective snapshot.js',
    body: '',
    choices: ['Yes', 'No'],
    start: 1638799933,
    end: 1639144255,
    snapshot: 13939561,
    network: '1',
    strategies: JSON.stringify([{"name":"erc721","params":{"symbol":"EQZ","address":"0x1Da87b114f35E1DC91F72bF57fc07A768Ad40Bb0","decimals":18}}]),
    plugins: JSON.stringify({}),
    metadata: JSON.stringify({})
  });
  console.log(receipt);
```
- proposal `type` must be `single-choice`
- `proposal strategies` must match `space strategies`
- **todo:** plugins? 

### Vote
```typescript
  const receipt = await client.vote(wallet, "0x3fcc740f22A19875647cb7C4A04D4801bB432C1A", {
    space: 'test.cartesian.eth',
    proposal: '0x1d63cee9c85a944858d021416ccb5685860ab99c4327f402bf4562b4d111f8ab',
    type: 'single-choice',
    choice: 2,
});
```
