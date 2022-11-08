## Multiwrap NFT Port For Filecoin VM

Original author:

Port: Titus Miles (Haze Ventures)

Tools Used: NFT.Storage, IPFS

Comments:

After testing, I found permissions errors in the base contract which prevents it from being publicly useable on Etherscan.

The minting roles required may be permissioned to an address defined by the thirdweb sdk.

Transaction hash of calling the wrap function to combine 2 NFTs, failed with permissions error: https://goerli.etherscan.io/tx/0xeb0440cf58c51f1d92c93a0a239c6550bc828f6e42ae13b5a40a557aa5c1f63f

If you follow the rules to use the thirdweb sdk, it should be useable with the current version of Filecoin VM, given you also use thirdweb sdk in the terminal along with the instructions given on: https://github.com/jimpick/wallaby-fevm-msg-signer, this contract's binary files are also already provided in the bin folder.

Demo showcases the wrapping functions being signed on etherscan, the permissioned functions likely being given to the thirdweb SDK is what is causing the transaction to fail.


Overall I think this is a promising tool, and is a good starting point to create a public version of the wrapper that can work for anyone calling the contract functions from the block explorer, without specifically having to use the sdk through a terminal for it to properly work.

**Compilation Instructions (Remix IDE Online)**

Loading the repository 

1. Activate the Sourcify Plugin in Remix IDE
2. Connect to Goerli Testnet via Metamask
3. Select Goerli Testnet from the Plugin's dropdown menu and Input the verified contract address 0x2771Ad8D23e65745F889A06543fB1E2D1Ab598Fe. Click Fetch Contract, after clicking some permissions requests to modify files the contract source repository should be fully loaded in Remix IDE.

    OR

  Download the Remix Workspace file given in the source folder and load it into Remix IDE.

4. Click Multiwrap.sol in the multiwrap folder, and compile it using the proper compiler version (also enable Optimization is required due to the contract size).

*Original Instructions:*

First, intall the required dependencies:

```bash
npm install
# or
yarn install
```

Then, run the development server:

```bash
npm run dev
# or
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `pages/index.js`. The page auto-updates as you edit the file.

On `pages/_app.js`, you'll find our `ThirdwebProvider` wrapping your app, this is necessary for our hooks to work.

on `pages/index.js`, you'll find the `useMetamask` hook that we use to connect the user's wallet to MetaMask, and the other hooks required for this demo.

# Multiwrap Example

## Introduction

In this guide, we will utilize the [**Multiwrap**](https://portal.thirdweb.com/typescript/sdk.multiwrap) contract.

Multiwrap lets you wrap any number of ERC20, ERC721 and ERC1155 tokens you own into a single wrapped token bundle.

The single wrapped token received on bundling up multiple assets, as mentioned above, is an ERC721 NFT. It can be transferred, sold on any NFT Marketplace, and generate royalties just like any other NFTs.

## Tools:

- [**thirdweb Multiwrap**](https://portal.thirdweb.com/typescript/sdk.multiwrap)

- [**thirdweb React SDK**](https://docs.thirdweb.com/react): The use of hooks such as [useMetamask](https://portal.thirdweb.com/react/react.usemetamask), [useAddress](https://portal.thirdweb.com/react/react.useaddress), [useNFTs](https://portal.thirdweb.com/react/react.usenfts) and a few others.

## Using This Repo

Create a clone of this repository using our CLI:

```bash
npx thirdweb create --example multiwrap-demo
```

### Getting the Multiwrap contract

```js
const multiwrap = useMultiwrap(multiwrapAddress);
```

### Giving permission to your multiwrap contract to move your tokens

```js
// Get the contracts
const erc20Contract = sdk.getToken(erc20TokenAddress);
const erc721Contract = sdk.getNFTCollection(erc721TokenAddress);
const erc1155Contract = sdk.getEdition(erc1155TokenAddress);

// Give permissions to the Multiwrap contract
await tokenContract.setAllowance(multiwrapAddress, 20);
await erc721Contract.setApprovalForToken(multiwrapAddress, tokenId);
await erc1155Contract.setApprovalForAll(multiwrapAddress, true);
```

### Wrapping Tokens

The SDK takes in a structure containing the tokens to be wrapped. This array is further grouped into the individual types of tokens.

```js
const tokensToWrap = {
  erc20Tokens: [
    {
      contractAddress: "0x.....",
      quantity: 20,
    },
  ],
  erc721Tokens: [
    {
      contractAddress: "0x.....",
      tokenId: "0",
    },
  ],
  erc1155Tokens: [
    {
      contractAddress: "0x.....",
      tokenId: "0",
      quantity: 1,
    },
  ],
};
```

We then pass these tokens to the contracts wrap function along with the NFT Metadata for our wrapped tokens.

```jsx
const nftMetadata = {
  name: "Wrapped bundle",
  description: "This is a wrapped bundle of tokens and NFTs",
  image: "ipfs://...",
};
```

```jsx
const tx = await multiwrapContract.wrap(tokensTowrap, nftMetadata);

const receipt = tx.receipt(); // the transaction receipt
const wrappedTokenId = tx.id; // the id of the wrapped token bundle
```

## Unwrapping Tokens

To unwrap tokens, we call [.unwrap](https://portal.thirdweb.com/typescript/sdk.multiwrap.unwrap). It will return the transaction receipt.

```jsx
await multiwrapContract.unwrap(wrappedTokenId);
```

## Get wrapped Contents

Get the contents of a wrapped token bundle. Will return a similar structure than the one passed in to the `wrap()` call.

```jsx
const contents = await multiwrapContract.getWrappedContents(wrappedTokenId);
console.log(contents.erc20Tokens);
console.log(contents.erc721Tokens);
console.log(contents.erc1155Tokens);
```

## Learn More

To learn more about thirdweb and Next.js, take a look at the following resources:

- [thirdweb React Documentation](https://docs.thirdweb.com/react) - learn about our React SDK.
- [thirdweb JavaScript Documentation](https://docs.thirdweb.com/react) - learn about our JavaScript/TypeScript SDK.
- [thirdweb Portal](https://docs.thirdweb.com/react) - check our guides and development resources.
- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.

You can check out [the thirdweb GitHub organization](https://github.com/thirdweb-dev) - your feedback and contributions are welcome!

## Join our Discord!

For any questions, suggestions, join our discord at [https://discord.gg/cd thirdweb](https://discord.gg/thirdweb).
