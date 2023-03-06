# Create your first multichain project 

### Overview
In this guide, you'll learn how to create a multichain NFT project minting site using WAGMI.sh and QuickNode. You'll be taken through the entire process, from selecting the appropriate blockchain to setting up smart contracts and designing a user interface that's easy to use and visually appealing.

With this guide, you'll gain the knowledge and skills you need to build your own dApp in the Web3 space, even if you're a complete beginner. Each step of the process is explained in detail, so you'll understand exactly what you're doing and why. By the end of the guide, you'll have a functional multichain NFT project minting site that you can be proud of.

We'll use the code available at:
https://github.com/irwingtello/quicknodeboilerplate. 

This project involves working with the following chains:
* Filecoin Virtual Machine
* Polygon
* Avalanche

It is possible to add additional chains with ease. We'll also be using Metamask for the connection.

### What We Will Do
* Set up an Polygon,Avalanche Node with QuickNode
* Create a navigation bar that detects the blockchain you are connected to and includes a button to disconnect.
* Create a component for minting NFTs across multiple blockchains.

### What You Will Need
* Nodejs installed (version 18.2.1 or higher)

* You need to install this npm packages:
    ```npm
    npm install ethers@^5.7.2 react@^18.2.0 react-router-dom@^6.8.1 react-scripts@5.0.1 usehooks-ts@^2.9.1 wagmi@^0.11.5 @mui/material@^5.11.8
    ```
* The address of the contract deployed on the different blockchains.
Javascript and React experience.

* Generate a smart minting contract.
    * You can generate that contract from here:
    https://docs.openzeppelin.com/contracts/4.x/wizard

    * Fund your testnet wallet
    You can get funds here:
        * https://faucet.quicknode.com/drip
        * FVM Faucet - https://hyperspace.yoga/#faucet

### What is Wagmi?
It is crucial to understand the tool that will be utilized before beginning the project. Wagmi is a set of React Hooks that offers all the necessary features for working with Ethereum. This tool makes it convenient to connect with a wallet, display ENS and balance information, sign messages, interact with contracts, and more.
Wagmi has been designed with efficiency in mind and features caching, request deduplication, and persistence to simplify the development process.


### Set up your environment
We will be using the latest version of these packages. To create our React project, follow these steps:
```npm
npx create-react-app quicknodeboilerplate
npm i wagmi ethers@^5
npm install react-router-dom
npm install @mui/material @emotion/react @emotion/styled
npm i usehooks-ts
```

In the src folder, we need to create a file named Chain.jsx. This file will be used to customize and set our QuickNode RPC. Wagmi provides configuration details for different chains on this link: https://github.com/wagmi-dev/references/tree/main/packages/chains/src.

It is important to double-check the configuration in the official blockchain documentation  from Avalanche,Polygon, Filecoin  to ensure you have the correct settings.

The following links provide configuration details for each of the chains:

* Avalanche: https://support.avax.network/en/articles/4626956-how-do-i-set-up-metamask-on-avalanche


* Polygon: https://wiki.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/


* Filecoin - Hyperspace Testnet (You can find information about Public Endpoints in the RPC section of the following link):
https://github.com/filecoin-project/testnet-hyperspace

It is recommended to replicate a similar pattern as follows:

```jsx
import { Chain } from './types'

export const avalanche = {
  id: 43_114,
  name: 'Avalanche',
  network: 'avalanche',
  nativeCurrency: {
    decimals: 18,
    name: 'Avalanche',
    symbol: 'AVAX',
  },
  rpcUrls: {
    default: { http: ['https://api.avax.network/ext/bc/C/rpc'] },
    public: { http: ['https://api.avax.network/ext/bc/C/rpc'] },
  },
  blockExplorers: {
    etherscan: { name: 'SnowTrace', url: 'https://snowtrace.io' },
    default: { name: 'SnowTrace', url: 'https://snowtrace.io' },
  }
}
```

ALT: Avalanche chain configuration.
However, a modification can be performed as depicted below:

```jsx
export const avalanche = {
    id: 43_114,
    name: 'Avalanche',
    network: 'avalanche',
    nativeCurrency: {
      decimals: 18,
      name: 'Avalanche',
      symbol: 'AVAX',
    },
    rpcUrls: {
      default: { http: [process.env.REACT_APP_AVALANCHE_MAINNET] },
    },
    blockExplorers: {
      etherscan: { name: 'SnowTrace', url: 'https://snowtrace.io/address/type/valuex' },
      default: { name: 'SnowTrace', url: 'https://snowtrace.io/address/type/valuex' },
    },
  }
```
ALT: Avalanche chain configuration - Customized to retrieve .env secrets

To take advantage of QuickNode, it is necessary to replace the RPC URL with an endpoint from QuickNode. The 'contracts' attribute has been removed and a specific RPC has been designated.

This configuration ensures stability and improves interaction with the blockchain. The block explorer feature has been improved to include the address suffix, as well as the 'type' and 'valuex' values, which can be adjusted to match the desired scenario.w

The code to implement QuickNode can be obtained from the repository located at:
 https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/Chain.jsx.

Finally, the designated RPC must be set and an endpoint must be established to match the specified configuration.


![Screenshot of the QuickNode platform for creating your RPC endpoint](https://github.com/irwingtello/quickNodeWagmi/blob/main/QuickNodeRPC.png)

To ensure optimal functionality of your Avalanche node, it is important to configure it correctly. The project has already implemented the necessary configuration and your Remote Procedure Call (RPC) should be in the following format:
https://{your-node-name}.quiknode.pro/{your-token}/ext/bc/C/rpc
For more information on this configuration, refer to the support article at
https://support.quicknode.com/hc/en-us/articles/6807695630737-Why-doesn-t-my-AVAX-endpoint-work

It is important to keep the secrets obtained from the RPC secure. These secrets should be stored in a .env file and kept confidential. The following environment variables must be defined with the appropriate secrets:
REACT_APP_FILECOIN_HYPERSPACE='Replace this'
REACT_APP_AVALANCHE_MAINNET='Replace this'
REACT_APP_AVALANCHE_FUJI='Replace this'
REACT_APP_POLYGON_MAINNET='Replace this'
REACT_APP_POLYGON_MUMBAI='Replace this'
Initializing wagmi in our project
Index.js
https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/index.js
The code below imports the required libraries from the Wagmi library:
import { MetaMaskConnector } from 'wagmi/connectors/metaMask'
import {createClient, configureChains} from 'wagmi';
import {jsonRpcProvider} from 'wagmi/providers/jsonRpc';
import {WagmiConfig} from 'wagmi';
import {hyperspaceTestnet, avalanche, avalancheFuji, polygon, polygonMumbai} from './Chain';
To properly configure the blockchain that the user will connect to, the function "configureChains" is used with the following parameters:

const {chains, provider} = configureChains(
  [hyperspaceTestnet, avalanche, avalancheFuji, polygon, polygonMumbai],
  [
    jsonRpcProvider({
      rpc: (chain) => {
        if (chain.id === hyperspaceTestnet.id) return {http: chain.rpcUrls.default};
        if (chain.id === avalanche.id) return {http: chain.rpcUrls.default};
        if (chain.id === avalancheFuji.id) return {http: chain.rpcUrls.default};
        if (chain.id === polygon.id) return {http: chain.rpcUrls.default};
        if (chain.id === polygonMumbai.id) return {http: chain.rpcUrls.default};
        return null;
      },
    }),
  ]
);

The following steps create a client and provider to establish a connection:
const client = createClient({
  autoConnect: true,
  connectors: [
	new MetaMaskConnector({chains}),
  ],
  provider: provider
})
Finally, to integrate the client into the component, the WagmiConfig component is utilized with the client and provider as parameters.
   <WagmiConfig client={client}>
    <Router>
        <Routes>
        <Route exact path='/' element={ <App chains={chains} />}></Route>
        </Routes>
       </Router>
    </WagmiConfig>
It is essential to include the client as a parameter when using the WagmiConfig component. The information about active chains can be accessed by passing the chains value as a property to another component, for example, the main component.

App.js
https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/App.js
The following wagmi hooks are imported in the App.js component:
import {
  useAccount,
  useNetwork
} from 'wagmi';
The functionality of these hooks is as follows:
useAccount: This hook returns the current chain account associated with the user's connected wallet.
useNetwork: This hook returns the current chain network the user is connected to, such as the mainnet network or a test network.

ALT: Navbar for connecting and disconnecting from the actual network
The props.chains are being iterated over to check if the specified blockchain is supported, or if the user's MetaMask wallet is not connected.
Navbar.js
https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/Profile/Navbar.js
In the Navbar.js component, we import the following hooks from wagmi:
import {
  useAccount,
  useConnect,
  useDisconnect,
  useNetwork,
} from 'wagmi'
In this section, we define the following variables using the hooks from wagmi:
address: This variable holds the address of the user's connected wallet, obtained from the useAccount hook.
connect: This variable holds the connect function from the useConnect hook. This function enables the user to connect to a blockchain.
connectors: This variable holds the available connectors for connecting to EVM networks. It is obtained from the useConnect hook.
isLoading: This variable holds the loading status of the EVM network connection. It is obtained from the useConnect hook.
pendingConnector: This variable holds the current pending connector for connecting to EVM networks. It is obtained from the useConnect hook.
disconnect: This variable holds the disconnect function from the useDisconnect hook. This function enables the user to disconnect from the EVM networks.
chain: This variable holds the current EVM network the user is connected to. It is obtained from the useNetwork hook.



  const { address } = useAccount()
  const { connect, connectors ,isLoading, pendingConnector } =useConnect()
  const { disconnect } = useDisconnect()
  const { chain } = useNetwork();
After we define these variables, we will use them in the following code.

ALT: NavBar directs you to the relevant block explorer.
The code checks if the props.isConnected value is true. If it is, the code first displays a Link component that shows the address value. The Link component has a onClick handler that opens the block explorer for the connected network in a new tab. It does this by replacing the URL with the address value.
Next, the code displays another Link component that shows the name of the connected network. If the network is not supported, the component displays "Network not supported". If the chain value is not defined, the component displays "Chain is undefined".
Finally, the code displays a Button component with the label "Disconnect". When clicked, it triggers the disconnect function.
If props.isConnected is false, the code maps over an array of connectors and displays a Button component for each connector. The Button component has a onClick handler that triggers the connect function with the connector as an argument. The button's label is "Connect: connector.name". If the connector is not ready, the button is disabled and labeled with "(unsupported)". If the connector is currently being connected, the button label will show "(connecting)".
To summarize, this code uses the value of props.isConnected to determine whether to render a set of components for a connected network or for available connectors that can be used to connect to a network.


ALT: Retrieve the name of the connector
Mint.js
https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/Mint.js
In the Mint.js component, the following wagmi hooks are imported:
import {
  useAccount,
  usePrepareContractWrite,
  useContractWrite,
  useWaitForTransaction,
  useNetwork
} from "wagmi";

Additionally, the Application Binary Interface (ABI) of the associated smart contract must be imported and saved in JSON format in order to facilitate interaction with the safeMint function.

ALT: ABI stored in the Contract Folder


ALT : Obtaining the ABI from Remix.
To obtain the ABI for your smart contract, you can use the Remix Ethereum online platform at "https://remix.ethereum.org/".
First, retrieve the deployed contract address for each network in which you want to perform the minting operation. Then, save the address of the deployed smart contract in the following format:
REACT_APP_SMART_CONTRACT_AVALANCHE_FUJI='Smart contract address'
REACT_APP_SMART_CONTRACT_POLYGON_MUMBAI='Smart contract address'
REACT_APP_SMART_CONTRACT_FILECOIN_HYPERSPACE='Smart contract address'
In your code, you will also need to define the following variables using the hooks from wagmi:
  const { isConnected } = useAccount()
  const { chain } = useNetwork()
Furthermore, it is also necessary to import the useDebounce hook from usehooks-ts for future usage.
import { useDebounce } from 'usehooks-ts'
In the subsequent step, a custom function named addressSmartContract is created with the purpose of extracting the address of the smart contract from the active blockchain.
Please import that function in your code:
https://github.com/irwingtello/quicknodeboilerplate/blob/master/src/functions.js
import {addressSmartContract} from "./functions"

ALT : Function for retrieving the actual smart contract address from the blockchain.


After we set up the important variables, we can move forward with the following steps:
Initialize the state variable textBoxes with an array of objects that contain the imageURLs, as well as the state variables uriField and addressField for capturing user input.
Implement the useDebounce hook to debounce the addressField and uriField state variables, in order to improve performance.
Use the useAccount and useNetwork hooks to obtain information about the user's account and network, respectively.
Use the usePrepareContractWrite hook to prepare the smart contract function safeMint for execution, with the debounced addressField and debouncedUriField as parameters.
Invoke the useContractWrite hook to execute the safeMint function, with the result stored in the dataCW state variable.
Monitor the transaction's status with the useWaitForTransaction hook.

ALT : Implementation of hooks from wagmi to complete the minting process
Before calling the writeCW?.() function, it is necessary to ensure that the addressField and uriField state variables have been assigned values. Then, the writeCW?.() function can be executed.

ALT : Initiating the mint function from wagmi

Congratulations! You have successfully built your first multi-chain NFT project. I hope it was a great learning experience and that you will continue to develop your skills in the exciting field of blockchain technology.

ALT: A screenshot of the mint component.
You can learn more about wagmi here:
https://wagmi.sh/react/getting-started


We ❤️ Feedback!
If you have any feedback or questions on this guide, let us know. Or, feel free to reach out to us via Twitter or our Discord community server. We’d love to hear from you! 
=====Links to social networks ======
URLs 

Twitter: https://twitter.com/irwingtello
LinkedIn: https://www.linkedin.com/in/irwingtello/
Email: irwing@dfhcommunity.com






