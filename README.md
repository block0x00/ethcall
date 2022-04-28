# Note

This package is forked and modified from [Destiner package](https://github.com/Destiner/ethcall).

# ethcall

Utility library to make calls to Ethereum blockchain.

Uses MakerDAO's [Multicall contracts](https://github.com/makerdao/multicall) to make multiple requests in a single HTTP query. Encodes and decodes data automatically.

Powered by [abi-coder](https://github.com/Destiner/abi-coder) and [ethers.js](https://github.com/ethers-io/ethers.js/).

```
npm install bkc-ethcall
```

## API

* `Contract(address, abi)`: create contract instance; calling `contract.call_func_name` will yield a `call` object.
* `all(calls)`: execute all calls in a single request.
* `tryAll(calls)`: execute all calls in a single request. Ignores reverted calls and returns `null` value in place of return data (wrapper on top of `tryAggregate` method).
* `tryEach(calls, canFail)`: execute all calls in a single request. Ignores reverted calls and returns `null` value in place of return data for the calls that are allowed to fail (wrapper on top of `aggregate3` method).
* `calls`: list of helper call methods
  * `getEthBalance(address)`: returns account ether balance

## Example

```js
import { InfuraProvider } from '@ethersproject/providers';
import { Contract, Provider } from 'bkc-ethcall';

import erc20Abi from './abi/erc20.json';

const infuraKey = 'INSERT_YOUR_KEY_HERE';
const provider = new InfuraProvider('mainnet', infuraKey);

const daiAddress = '0x6b175474e89094c44da98b954eedeac495271d0f';

async function call() {
  const ethcallProvider = new Provider();
  await ethcallProvider.init(provider);

  const daiContract = new Contract(daiAddress, erc20Abi);

  const uniswapDaiPool = '0x2a1530c4c41db0b0b2bb646cb5eb1a67b7158667';

  const ethBalanceCall = ethcallProvider.getEthBalance(uniswapDaiPool);
  const daiBalanceCall = daiContract.balanceOf(uniswapDaiPool);

  const data = await ethcallProvider.all([ethBalanceCall, daiBalanceCall]);

  const ethBalance = data[0];
  const daiBalance = data[1];

  console.log('eth balance', ethBalance.toString());
  console.log('dai balance', daiBalance.toString());
}

call();
```

## Contributing

All Mulitcall contracts are stored in `src/multicall.ts` file. There are three `getMulticall` methods corresponding to three Multicall versions. To add a new contract, you need to know its version, address, chain id of the underlying chain, and (optionally) block at which the Multicall contract was deployed.

## Deployless Multicall

If you query a chain on which Multicall is not deployed, or if you query a historical block before the deployment of the contract, the deployless version will be used instead. In short, deployless Multicall "emulates" the deployed contract and returns the exact same data. Note that you can't query ETH balance using deployless version.

You can read more about deployless Multicall [here](https://insights.magmatic.xyz/posts/deployless-multicall/).

# Modified
Adding Bitkub Chain Mainnet and Testnet into this project.

### BKC Testnet
```
Multicall: 0x9C68D1017157339Fd6Bb53898A1EE5227A2fefEc
Multicall2: 0xe25e43ac2Cee8C92880E624b036933d1243B02bA
Multicall3: 0x012b6339091A50CDA954183B483c41dc2B85a3C7
```

### BKC Mainnet
```
Multicall: 0x3eb9f89B34ac5641B8170b838F3ADd054da9526e
Multicall2: 0x23a5803AED326B0b1e809100bF34C1f8E9eb6523
Multicall3: 0xd504F38130E8d0C2409F7bEA658dcfE0b29646Ca
```
