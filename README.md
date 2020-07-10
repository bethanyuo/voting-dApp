# Voting dApp with Client-Side Wallet
This is an implementation of a simple Voting DApp with client-side Ethereum Wallet, based on Solidity Smart Contract and client-side UI (JS + jQuery + ethers.js) + server-side account management (Node.js + Express + simple REST API).

## Requirements
*	Node 	v13.5.0
*	NPM 	v6.13.

## Network
Ropsten Testnet

## Goal
*	The app implements `register / login / logout / vote` functionality.
*	Clients vote for given candidates.
*	Candidates and votes are stored in Solidity Contract on the Ropsten Ethereum Testnet.
*	Users register in the system and get a newly created wallet (private key + address).
    *	The wallet is stored in JSON encrypted format at the server side.
    *	Wallet password / private key never leave the client app.
*	Uses different passwords for server login and for the wallet:
    *	Server password: HMAC (username, password)
    *	Wallet password: user's original password
*	Wallet is encrypted in UTC/JSON format (both private key + mnemonic phrase are encrypted with `AES-CRT-128` using Scrypt key derivation).
*	Upon log in the client-side downloads the encrypted JSON from the server and stores it in the browser session.
 
## Setup the Project
Download the Skeleton zip file from today's topic and extract it. Open the project. Run npm install to download the necessary dependencies. You will see that the server is ready, the client-side HTML and CSS as well, only the client.js file is not completely finished. 
Get to know the code.
 
## Deploy the contract
Before we continue with the client-side, let's deploy the contract in Ropsten. Create a simple Voting contract, which will store candidates and users can add and vote for candidates.
 
After you have written the contract, upload the contract in Ropsten through Remix.
 
After the transaction is mined the contract will appear.
 
Now, add two or three candidates with `addCandidate`.
 
## Register
After you have added some candidates, from Remix copy the contract address and ABI and set them in the client.js.
 
Contract address:
```
0xc0ddf591d1a7439f86c295d50e24ed9f5b8d1e75
```
Contract ABI:
```json
[
	{
		"constant": false,
		"inputs": [
			{
				"name": "name",
				"type": "string"
			}
		],
		"name": "addCandidate",
		"outputs": [],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"constant": false,
		"inputs": [
			{
				"name": "index",
				"type": "uint32"
			}
		],
		"name": "vote",
		"outputs": [],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "function"
	},
	{
		"constant": true,
		"inputs": [],
		"name": "candidatesCount",
		"outputs": [
			{
				"name": "",
				"type": "uint32"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	},
	{
		"constant": true,
		"inputs": [
			{
				"name": "index",
				"type": "uint32"
			}
		],
		"name": "getCandidate",
		"outputs": [
			{
				"name": "",
				"type": "string"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	},
	{
		"constant": true,
		"inputs": [
			{
				"name": "index",
				"type": "uint32"
			}
		],
		"name": "getVotes",
		"outputs": [
			{
				"name": "",
				"type": "uint32"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	}
]
```
Then, create a contract instance using the address, ABI and provider.
 
Now let's create the register method. Take the username and wallet password with jQuery.  
Then, create a new wallet using ethers.Wallet.createRandom and encrypt it with the password. Of course, catch errors and show them using showError and finally hide the progress bar.
 
After that create the back-end password using `CryptoJS.HmacSHA256` with the username and wallet password as arguments.
 
Now it's time to make a POST request to the server. Create it using `jQuery.ajax()` with the following parameters:
* type – POST
* url – `/register`
* data – JSON of the username
* server password
* the encrypted wallet
* content type – `application/json`
 
After the request is done, save the username and encrypted wallet in the session storage and show the home page using `showView("viewHome")`. Show an additional message that the user has been successfully registered and show the mnemonic for the first and last time.
 
Now test the register functionality.  Go to src folder and in the terminal type:
```bash
$ node voting-server.js
```
 
Open the browser and go to localhost:
```
http://localhost:1024
```
 
As you can see, the voting results are loading since they have not been implemented yet. Step by step. Go to [Register] and create a registration.
 
## Logout
Create the simple logic of the logout functionality, which will clear the session storage and show the home view.

## Login
Now it is time to implement the login functionality. Like the register one, take the username and wallet password and create the back-end password using `HMAC256`. Then, make a POST request to `/login` with the username and password as data. The server-side will validate whether the credentials are correct or not and return a status message. If they are correct, the result will store the user's encrypted wallet. Save it in the session storage, along with the username. Last but not least, show the home view and an appropriate message.
 
Now, it is time to test the functionality. Log in with the previously created user.
 
## Voting Results
Now it is time to use the contract instance and get the candidates. Go to `loadVotingResults` in client.js. In a `try-catch` block, first get the count of the candidates using `votingContract.candidatesCount()`. After we have got the count, with a simple for cycle get the candidate and his votes by an index and store them in an array.
 
After that comes the part to display them. For each candidate, create a `<li>` element with its candidate name and votes. Additionally, if a user is logged in, a button will appear with which he can vote. Append the `<li>` element to the `<ul>` with id `#votingResults`.
 
Now it's time to test the functionality. Refresh the home page.

## Vote
Last but not least, the voting functionality. The method takes the candidate's index and the candidate's name as parameters. We will decrypt the wallet and vote for the candidate.
Take the wallet from the session storage and create a prompt box in which the user will re-enter the password.
 
Using `ethers.Wallet.fromEncryptedWallet`, decrypt the JSON file. Create a new wallet with the private key and Ropsten provider. Then create a new voting contract instance with the wallet as a signer.
 
Vote for the candidate. After that, show a message with the candidate's name and a URL of the transaction in Ropsten block explorer with the received transaction hash as callback.

Before you test the vote functionality, get yourself some test ETH from the [faucet](http://faucet.ropsten.be:3001/) or ask someone to send you. 

Also, you can use your mnemonic to view keys and addresses using a tool like [iancoleman](https://iancoleman.io/bip39/). From there, you can copy the first address you find from the derived addresses to receive ether using this faucet or import the first account’s private key into an external wallet such as Metamask to receive funds using this faucet.

After that, pick one of your candidates and click [Vote]. A pop-up will appear to re-enter your password. After that a message/error will appear.

## Module
MI4: Module 2: E2
