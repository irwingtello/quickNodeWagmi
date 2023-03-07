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

To complete the configuration, you must set the designated RPC and establish an endpoint that corresponds to the specified configuration. The following configurations are used in this tutorial:
* Avalanche Mainnet
* Avalanche Testnet
* Polygon Mainnet
* Polygon Testnet


![Screenshot of the QuickNode platform for creating your RPC endpoint](https://github.com/irwingtello/quickNodeWagmi/blob/main/QuickNodeRPC.png)

To ensure optimal functionality of your Avalanche node, it is important to configure it correctly. The project has already implemented the necessary configuration and your Remote Procedure Call (RPC) should be in the following format:
```jsx
https://{your-node-name}.quiknode.pro/{your-token}/ext/bc/C/rpc
```

For more information on this configuration, refer to the support article at:

https://support.quicknode.com/hc/en-us/articles/6807695630737-Why-doesn-t-my-AVAX-endpoint-work

It is important to keep the secrets obtained from the RPC secure. These secrets should be stored in a .env file and kept confidential. The following environment variables must be defined with the appropriate secrets:
```.env
REACT_APP_FILECOIN_HYPERSPACE='Replace this'
REACT_APP_AVALANCHE_MAINNET='Replace this'
REACT_APP_AVALANCHE_FUJI='Replace this'
REACT_APP_POLYGON_MAINNET='Replace this'
REACT_APP_POLYGON_MUMBAI='Replace this'
```

### Initializing wagmi in our project
#### Index.js
Now, let's open up the index.js file and input the following code:
```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import './index.css';
import App from "./App";
import { BrowserRouter } from "react-router-dom";
import { MetaMaskConnector } from 'wagmi/connectors/metaMask'
import { publicProvider } from 'wagmi/providers/public'
import { Provider,createClient,configureChains,useAccount, useConnect, useDisconnect } from 'wagmi'
import { jsonRpcProvider } from 'wagmi/providers/jsonRpc'
import { Chain } from 'wagmi'
import { useNetwork } from 'wagmi'
import { WagmiConfig } from 'wagmi'
import {hyperspaceTestnet,avalanche,avalancheFuji,polygon,polygonMumbai} from './Chain'
import {
  BrowserRouter as Router,
  Routes,
  Route
} from 'react-router-dom';

const { chains, provider } = configureChains(
  [ hyperspaceTestnet,avalanche,avalancheFuji,polygon,polygonMumbai],
  [
    jsonRpcProvider({
      rpc: (chain) => {
        if (chain.id === hyperspaceTestnet.id) return { http: chain.rpcUrls.default  };
        if (chain.id === avalanche.id) return { http: chain.rpcUrls.default };
        if (chain.id === avalancheFuji.id) return { http: chain.rpcUrls.default };
        if (chain.id === polygon.id) return { http: chain.rpcUrls.default };
        if (chain.id === polygonMumbai.id) return { http: chain.rpcUrls.default };
        return null;
      },
    }),
  ]
);

const client = createClient({
  autoConnect: true,
  connectors: [
    new MetaMaskConnector({ chains }),
  ],
  provider:provider
})

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
 
    <WagmiConfig client={client}>
    <Router>
        <Routes>
        <Route exact path='/' element={ <App chains={chains} />}></Route>
        </Routes>
       </Router>
    </WagmiConfig>

);
```
Let's go over the code:

Lines 6-12: Import dependencies from Wagmi.sh.

Line 13: Imports the blockchain configuration we defined in the last steps

Lines 19-33: Configure the blockchains to which the user will connect.

Lines 35-41: Create a client and provider to establish a connection.

Lines 46-52: Integrate the client into the component, the WagmiConfig component is utilized with the client and provider as parameters. 

Note: It is essential to include the client as a parameter when using the WagmiConfig component. The information about active chains can be accessed by passing the chains value as a property to another component, for example, the main component.

#### App.js

```jsx
import React,{useEffect,useState} from 'react';
import { ethers } from 'ethers';
import {
  Provider,createClient,configureChains, useConnect, useDisconnect,
  useAccount,
  usePrepareContractWrite,
  useNetwork,useContractRead, useSwitchNetwork,WagmiConfig
} from 'wagmi'
import Navbar from './Profile/Navbar';
import Mint from './Mint';
function App(props) {
  const { address, connector, isConnected } = useAccount()
  const { chain } = useNetwork()
  return (
    <React.Fragment>
      <Navbar component="nav" isConnected={isConnected} chains={props.chains}></Navbar>
      {
      chain ? props.chains.find(networkValue => chain.id === networkValue.id) ? isConnected ?
              <React.Fragment>
              <Mint isConnected={isConnected} chains={props.chains}></Mint>
              </React.Fragment>:
              <React.Fragment>
              </React.Fragment>
      : "Network not supported" : "Chain is undefined"}
  

</React.Fragment>
  );
}

export default App;
```
Lines 3-8: Import dependencies from Wagmi.sh.

Line 5: This hook returns the current chain account associated with the user's connected wallet.

Line 7: This hook returns the current chain network the user is connected to, such as the mainnet network or a test network.

Lines 16-18: The props.chains are being iterated over to check if the specified blockchain is supported, or if the user's MetaMask wallet is not connected.

#### Navbar.js
```jsx
import React, { useEffect, useState,useContext } from "react";
import AppBar from '@mui/material/AppBar';
import Box from '@mui/material/Box';
import Button from '@mui/material/Button';
import Card from '@mui/material/Card';
import CardActions from '@mui/material/CardActions';
import CardContent from '@mui/material/CardContent';
import CardHeader from '@mui/material/CardHeader';
import CssBaseline from '@mui/material/CssBaseline';
import Grid from '@mui/material/Grid';
import Toolbar from '@mui/material/Toolbar';
import Typography from '@mui/material/Typography';
import Link from '@mui/material/Link';
import GlobalStyles from '@mui/material/GlobalStyles';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import blue from '@mui/material/colors/blue';
import { purple } from '@mui/material/colors';
import Menu from '@mui/material/Menu';
import MenuItem from '@mui/material/MenuItem';
import { ethers, getDefaultAccount } from "ethers";
import { BrowserRouter, Routes,Route, Switch } from 'react-router-dom';
import {
  useAccount,
  useConnect,
  useDisconnect,
  usePrepareContractWrite,
  useNetwork, useSwitchNetwork
} from 'wagmi'
import { useNavigate } from 'react-router-dom';
const theme = createTheme({
    palette: {
      primary: {
        main: "#ffffff",
      },
      secondary: {
        main: "#ffffff",
      },
    },
  });
function Navbar(props) {
  const { ethereum } = window;
  const { address, connector, isConnected } = useAccount()
  const { connect, connectors, error, isLoading, pendingConnector } =useConnect()
  const { disconnect } = useDisconnect()
  const { chain } = useNetwork()
  // Create a stateful variable to store the network next to all the others
  const [network, setNetwork] = useState('');


 
    const [defaultAccount, setDefaultAccount] = useState(null);
  useEffect(() => {

  }, [defaultAccount]);
  const [anchorEl, setAnchorEl] = React.useState(null);
  const open = Boolean(anchorEl);
  const handleClick = (event) => {
    setAnchorEl(event.currentTarget);
  };
  const handleClose = () => {
    setAnchorEl(null);
  };
  const navigate = useNavigate();
  const home = async () => {
    window.open('https://www.quicknode.com/', '_blank');
  }
  return (
          <ThemeProvider theme={theme}>
      <AppBar
        position="static"
        color="default"
        elevation={0}
        sx={{ borderBottom: (theme) => `1px solid ${theme.palette.divider}` }}
      >
        <Toolbar sx={{ flexWrap: 'wrap' ,background: "#205295",textColor:"#"}}>
          <Typography variant="h6" noWrap sx={{ flexGrow: 1 }}  color="secondary">
          <Link  onClick={home}>QuickNode</Link>
          </Typography>
          {props.isConnected==true?
          
          <React.Fragment>
                        <Link
              variant="button"
              color="secondary" 
              onClick={() => {
                if (chain) {
                  if (props.chains.find(networkValue => chain.id === networkValue.id)) {
                    let explorer = props.chains.find(networkValue => chain.id === networkValue.id).blockExplorers.default.url.replace("type", "address").replace("valuex", address);
                    window.open(explorer,'_blank');
                  }
                }
				
                  
              }}
              sx={{ my: 1, mx: 1.5 }}
            >
              {address}
            </Link>
                        <Link
              variant="button"
              color="secondary"

              sx={{ my: 1, mx: 1.5 }}
            >
              {
                  props.chains.some(networkValue => {
                    props.chains.find(chainx => chain.id === networkValue.id)
                  }
                    )
                  

              }
            {chain ? props.chains.find(networkValue => chain.id === networkValue.id) ? "Connected to:" + chain.network : "Network not supported" : "Chain is undefined"}
            </Link>
      <Menu
        id="basic-menu"
        anchorEl={anchorEl}
        open={open}
        onClose={handleClose}
        MenuListProps={{
          'aria-labelledby': 'basic-button',
        }}
      >
        
        <MenuItem >MINT</MenuItem>
    
      </Menu>
       
              <Button onClick={disconnect} variant="outlined" sx={{ my: 1, mx: 1.5 }}>
              Disconnect
            </Button>
          </React.Fragment>:
          <React.Fragment>
      {connectors.map((connector) => (
             <Button
              disabled={!connector.ready}
              key={connector.id}
              variant="outlined"
              onClick={() => connect({ connector })}
            >
              Connect: {connector.name}
              {!connector.ready && ' (unsupported)'}
              {isLoading &&
                connector.id === pendingConnector?.id &&
                ' (connecting)'}
            </Button>
          ))}
            </React.Fragment>
          }
          
   
        </Toolbar>
      </AppBar>
      </ThemeProvider>

  );
}
export default Navbar;
```
Lines 22-28: Import dependencies from Wagmi.sh.

Line 42: The address variable holds the address of the user's connected wallet, obtained from the useAccount hook.

Line 43: The connect variable holds the connect function from the useConnect hook. This function enables the user to connect to a blockchain.

Line 43: The connectors variable holds the available connectors for connecting to EVM networks. It is obtained from the useConnect hook.

Line 43: This isLoading variable holds the loading status of the EVM network connection. It is obtained from the useConnect hook.

Line 43: This pendingConnector variable holds the current pending connector for connecting to EVM networks. It is obtained from the useConnect hook.

Line 44: This disconnect variable holds the disconnect function from the useDisconnect hook. This function enables the user to disconnect from the EVM networks.

Line 45: This chain variable holds the current EVM network the user is connected to. It is obtained from the useNetwork hook.

Lines 82-98: These lines checks if the props.isConnected value is true. If it is, the code first displays a Link component that shows the address value. The Link component has a onClick handler that opens the block explorer for the connected network in a new tab. It does this by replacing the URL with the address value.

Lines 99-114: Shows the name of the connected network. If the network is not supported, the component displays "Network not supported". If the chain value is not defined, the component displays "Chain is undefined".

Lines 141-143: When Disconnect button is clicked, it triggers the disconnect function.

Lines 146-159: If props.isConnected is false, the code maps over an array of connectors and displays a Button component for each connector. 

The Button component has a onClick handler that triggers the connect function with the connector as an argument. The button's label is "Connect: connector.name". If the connector is not ready, the button is disabled and labeled with "(unsupported)". If the connector is currently being connected, the button label will show "(connecting)".

To summarize, this code uses the value of props.isConnected to determine whether to render a set of components for a connected network or for available connectors that can be used to connect to a network.

#### Mint.js

```jsx
import Navbar from './Profile/Navbar';
import React,{useEffect,useState,useMemo} from 'react';
import { ethers } from 'ethers';
import {
  useAccount,
  usePrepareContractWrite,
  useContractWrite,
  useWaitForTransaction,
  useNetwork
} from "wagmi";
import ABI from "./Contracts/MyToken.json";
import Box from '@mui/material/Box';
import Container from '@mui/material/Container';
import TextField from '@mui/material/TextField';
import Button from "@mui/material/Button";
import Avatar from '@mui/material/Avatar';
import Stack from '@mui/material/Stack';
import { useDebounce } from 'usehooks-ts'
import {addressSmartContract} from "./functions"
function Mint(props) {
  const [textBoxes, setTextBoxes] = useState(
    [
      { image:"https://nftstorage.link/ipfs/bafybeicx7pkobpcko425usyxdaxqk77pjszjywh44jtr6bq3d5the4cr3m/poh%20(7).jpg"  },
    { image:"https://nftstorage.link/ipfs/bafybeicx7pkobpcko425usyxdaxqk77pjszjywh44jtr6bq3d5the4cr3m/poh%20(6).jpg"  },
    { image:"https://nftstorage.link/ipfs/bafybeicx7pkobpcko425usyxdaxqk77pjszjywh44jtr6bq3d5the4cr3m/poh%20(5).jpg"  },
      {image:"https://nftstorage.link/ipfs/bafybeicx7pkobpcko425usyxdaxqk77pjszjywh44jtr6bq3d5the4cr3m/poh%20(3).jpg"}
  ]);
  const { address, connector, isConnected } = useAccount()
  const { chain } = useNetwork()
  const [uriField, setUriField] = useState("");
  const [addressField, setAddressField] = useState("");
  const changeUriField  = async (event) => {
    setUriField(event.target.value);
  }
  const selectUri  = async (uri) => {
    setUriField(uri);
  }

  const changeAddressField  = async (event) => {
    setAddressField(event.target.value);
  }
  const theFlag = useMemo(() => {
    return addressField !== "" && uriField !== "";
  }, [addressField, uriField]);
  const debouncedAddressField = useDebounce(addressField);
  const debouncedUriField = useDebounce(uriField)

  const {
    config:isConfig,
    data: datax,
    isSuccess: isSuccessPrepare,
    error: prepareError,
    isPrepareError: isPrepareError,
  } = usePrepareContractWrite({
    address:addressSmartContract(props.chains.find(networkValue => chain.id === networkValue.id).id),
    abi: ABI,
    functionName: "safeMint",
    enabled: theFlag,
    args: [debouncedAddressField,debouncedUriField],
    chainId:props.chains.find(networkValue => chain.id === networkValue.id).id,
    onSuccess(data) {
      console.log("Success", data);
    },
    onError(error) {
      console.log("Error", error);
    },
    onSettled(data, error) {
      console.log("Settled", { data, error });
    },
  });
  const { data:dataCW, error:errorCW, isError:isErrorCW, write:writeCW } = useContractWrite(isConfig);
  const { isLoading:isLoadingWT, isSuccess:isSuccessWT } = useWaitForTransaction({
    hash: dataCW?.hash,
  });
  const mint = (e) => {
    e.preventDefault()
    writeCW?.();
  }
  return (
    <React.Fragment>
      {
      chain ? props.chains.find(networkValue => chain.id === networkValue.id) ? isConnected ?
              <React.Fragment>
                     <Box
      sx={{
        my: 4,
        mx: 12,
        display: "flex",
        flexDirection: "column",
        alignItems: "center",
      }}
    >

      <div className="mint">
        <React.Fragment>
          <Container sx={{ py: 0 }} maxWidth="md">
            <center>
          <h1>Mint Badge</h1>
          <Stack direction="row" spacing={4}>
          {textBoxes.map((textBox, index) => (
          <Avatar src={textBox.image} key={index} onClick={()=> selectUri(textBox.image)}  sx={{ width: 100, height: 100 }}/>
            ))
          }

          </Stack>
          <TextField
                margin="normal"
                fullWidth
                sx={{ mt: 3, mx: 0 }}
                id="beneficiaryAddress"
                label="Beneficiary address"
                InputLabelProps={{ shrink: true }}
                type="text"
                name="beneficiaryAddress"
                value={addressField}
                onChange={changeAddressField}  
              />  
                                        <TextField
                margin="normal"
                fullWidth
                sx={{ mt: 3, mx: 0 }}
                id="URI"
                label="URI"
                value={uriField}
                onChange={changeUriField}  
                InputLabelProps={{ shrink: true }}
                type="text"
                name="URI"
              /> 
                <Button
                variant="contained"
                color="success"
                className="buttonWallet"
                sx={{ mt: 3, mx: 0 }}
                onClick={(e) =>{
                  e.preventDefault();
                  mint(e);
                }
                  
                }
              >
                MINT
              </Button>
              {
              dataCW?.hash &&(
              <React.Fragment> <br></br><a target="_blank" href={`${props.chains.find(networkValue => chain.id === networkValue.id).blockExplorers.default.url.replace("type", "tx").replace("valuex", dataCW.hash)}`}>Hash</a></React.Fragment>
              )
              }
              </center>
          </Container>
        </React.Fragment>
      </div>
    </Box>
              </React.Fragment>:
              <React.Fragment>
              </React.Fragment>
      : "" : ""}
  </React.Fragment>
  );
}

export default Mint;
```

Lines 4-10: Import dependencies from Wagmi.sh.

Line 11: The Application Binary Interface (ABI) of the associated smart contract must be imported and saved in JSON format in order to facilitate interaction with the safeMint function.
  To obtain the ABI for your smart contract, you can use the Remix Ethereum online platform at:

  https://remix.ethereum.org/

  Example: 

  ![Obtaining the ABI from Remix.](https://github.com/irwingtello/quickNodeWagmi/blob/main/SmartContractABI.png)

  After that , you need to save the contract address for each network in which you want to perform the minting operation. Then, save the address of the deployed smart contract in the following format:
  ```.env
  REACT_APP_SMART_CONTRACT_AVALANCHE_FUJI='Smart contract address'
  REACT_APP_SMART_CONTRACT_POLYGON_MUMBAI='Smart contract address'
  REACT_APP_SMART_CONTRACT_FILECOIN_HYPERSPACE='Smart contract address'
  ```
Line 18: Import the useDebounce hook from usehooks-ts.

Line 19: Import the function named addressSmartContract is created with the purpose of extracting the address of the smart contract from the active blockchain.

We recommend to create the function in specific folder called "functions.js",to have better organization of your code.

  ```jsx
  export function addressSmartContract(chainId) 
  {
      switch (chainId) {
          case 80_001:
              return process.env.REACT_APP_SMART_CONTRACT_POLYGON_MUMBAI;
          case 43_113:
          return process.env.REACT_APP_SMART_CONTRACT_AVALANCHE_FUJI;
          case 314_1:
          return process.env.REACT_APP_FILECOIN_HYPERSPACE;
          default:
            return 0;
        }
  }
  ```
Lines 21-27: Initialize the state variable textBoxes with an array of objects that contain the imageURLs.

Line 28: Define the isConnected variable using the useAccount hook from wagmi.

Line 29: Define the chain variable using the useNetwork hook from wagmi.

Lines 30-31: Initialize the state variables uriField and addressField for capturing user input.

Line 45: Use the usePrepareContractWrite hook to prepare the smart contract function safeMint for execution, with the debounced addressField and debouncedUriField as parameters.

Lines 45-46: Here we implement the useDebounce hook to debounce the addressField and uriField state variables, in order to improve performance.

Line 55: This function returns the current blockchain.

Line 71: Invoke the useContractWrite hook to execute the safeMint function, with the result stored in the dataCW state variable.

Line 72: Monitor the transaction's status with the useWaitForTransaction hook.

Line 77: Before calling the writeCW?.() function, it is necessary to ensure that the addressField and uriField state variables have been assigned values. Then, the writeCW?.() function can be executed.

Congratulations! You have successfully built your first multi-chain NFT project. I hope it was a great learning experience and that you will continue to develop your skills in the exciting field of blockchain technology.

![A screenshot of the mint component](https://github.com/irwingtello/quickNodeWagmi/blob/main/QuickNodeMint.png)

You can learn more about wagmi here:
https://wagmi.sh/react/getting-started


We ❤️ Feedback!
If you have any feedback or questions on this guide, let us know. Or, feel free to reach out to us via Twitter or our Discord community server. We’d love to hear from you! 

Twitter: 
https://twitter.com/irwingtello

LinkedIn: 
https://www.linkedin.com/in/irwingtello/

Email: 
irwing@dfhcommunity.com






