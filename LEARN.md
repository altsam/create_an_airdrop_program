# Create an airdrop program with Solana web3.js

Welcome to all the learners to the Solana 101 airdrop program development quest. In this quest, you’ll learn how to develop an application leveraging the Solana blockchain - which is the fastest growing blockchain community in the world.
We’ll be developing an airdrop program throughout this quest. Airdrop refers to the act of sending a cryptocurrency to a wallet, usually for free, to either promote the cryptocurrency or enable developers to test out transactions over the blockchain without spending real money.
This quest assumes that you’ve basic understanding of any one programming language - preferably Javascript. 

## Setting up your environment

Before we proceed, do make sure that you’ve node.js installed on your machine. To interact with the Solana blockchain, we need to connect to the Solana network. Solana provides us with a very handy JS package called `web3.js`. 
Let’s first set up our development environment with a functioning `web3.js` installation. To start a new node.js project, open your terminal in an empty repository and type in the following command.

```
npm init -y
```
This creates a file called `package.json` which is used to track all the npm packages i.e. node packages that will be used in your project. Let’s install the Solana `web3.js` package. To do so, type in the following command.
```
npm install --save @solana/web3.js
```
Congratulations, now you’re all set to start developing JS applications on Solana. 
**Note :The quest was written with @solana/web3.js v1.30.2.** If some of the functions in the quest do not work, go to package.json and edit the version field of web3.js in dependencies to “v1.30.2”. 

Generating a new wallet
To transact money on the blockchain, you need to use a software that facilitates these transactions called a wallet. Crypto wallets are physical devices or virtual programs that allow us to easily store and retrieve our crypto assets i.e. any cryptocurrencies that we might have on the blockchain.

Create a new file called `index.js` in your project directory. During this quest, we’ll be editing this file. Let’s import the required modules that we’ll be using soon. Add this to the newly created file..
```
const {
 Connection,
 PublicKey,
 clusterApiUrl,
 Keypair,
 LAMPORTS_PER_SOL,
 Transaction,
 Account,
} = require("@solana/web3.js");
```

The `Keypair` class that we just imported allows us to create a new wallet. Add the following lines in `index.js`
```
const newPair = new Keypair();
```
We now have a wallet object of type `Keypair` and will be airdropping SOL into this wallet.

## Storing the wallet credentials

Every crypto wallet has two components to it - a public key and a secret(aka private) key. The public key is used to uniquely identify your wallet over the blockchain and can be used to receive crypto to your wallet. The private key is used to perform transactions through your wallet. 
The `newPair` instance that we created in the previous section holds the public key and the secret key. 
Add the following line to `index.js`.

```
const publicKey = new PublicKey(newPair._keypair.publicKey).toString();
``` 

We’re extracting the public key from `accountInfo` and storing it in a new variable called `PublicKey` which is of type string.
We can do the same thing for a secret key. Add the following lines to your file.

```
const secretKey = newPair._keypair.secretKey
```

We’re extracting the private key from accountInfo and storing it in a new variable called `secretKey`, which is of type `Uint8Array` of length 64. 
**Never share the private key of your wallet with anyone**. It can be used to clone your wallet and perform transactions without your authorization.

## Creating the wallet balance function

What if we want to see the balance of our wallet? Now that we’ve seen how to create a wallet, let’s create a function that can utilize the public and private key and print out the wallet balance. Web3.js allows us to view the balance using the `getBalance` method inside the `connection` class that we had imported. Create an empty function signature like the one below.
```
const getWalletBalance = async () => {
    try {
    } catch (err) {
        console.log(err);
    }
};
```

Now, we’ll be adding functionality to `getWalletBalance`.
Add the following lines inside the ’try’ block.

```
const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
```

Creates a connection object that’ll be used to get the balance. Apart from the main network (called mainnet), Solana also maintains clusters called devnet and testnet. Devnet is the replica of the Solana’s mainnet, and serves as a playground for anyone who wants to try out the features of Solana. `clusterApiUrl` provides us the URL for devnet that we’ll be passing to create our connection object so that we get details of devnet.
Next, we’ll create a wallet object from the `secretKey`. Type the following command

```
const myWallet = await Keypair.fromSecretKey(secretKey);
```

We’ll now be querying the balance of this wallet.

```
const walletBalance = await connection.getBalance(
    new PublicKey(myWallet.publicKey)
);
console.log(`Wallet balance: ${walletBalance}`);
```

Finally, the combined function alongwith a few console logs looks like:
```
const getWalletBalance = async () => {
    try {
        const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
        const myWallet = await Keypair.fromSecretKey(secretKey);
        const walletBalance = await connection.getBalance(
            new PublicKey(myWallet.publicKey)
        );
        console.log(`=> For wallet address ${publicKey}`);
        console.log(`   Wallet balance: ${parseInt(walletBalance)/LAMPORTS_PER_SOL}SOL`);
    } catch (err) {
        console.log(err);
    }
};
```

## Airdropping SOL function!

Now that we’ve set up our wallet, let’s create a function that’ll airdrop some SOL into our wallet! Create an empty function called `airDropSol`, like the one below

```
const airDropSol = async () => {
    try {
    } catch (err) {
        console.log(err);
    }
};
```

As we had had done earlier, we need to create a `connection` object and a `walletKeyPair` object for the airdrop function. Add the following commands in the `airDropSol` function.

```
const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
const walletKeyPair = await Keypair.fromSecretKey(secretKey);
```

Now, we first create an airdrop signature using the wallet details and the amount of SOL we want to airdrop (you can airdrop at max 2SOL in one transaction). We then await a confirmation for the transaction from the network. Add the following lines to do so.
```
const fromAirDropSignature = await connection.requestAirdrop(
    new PublicKey(walletKeyPair.publicKey),
    2 * LAMPORTS_PER_SOL
);
await connection.confirmTransaction(fromAirDropSignature);
```

Congratulations! You’ve completed the airdrop function. Putting it all together with a few console logs turns out to be 
```
const airDropSol = async () => {
    try {
        const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
        const walletKeyPair = await Keypair.fromSecretKey(secretKey);
        console.log(`-- Airdropping 2 SOL --`)
        const fromAirDropSignature = await connection.requestAirdrop(
            new PublicKey(walletKeyPair.publicKey),
            2 * LAMPORTS_PER_SOL
        );
        await connection.confirmTransaction(fromAirDropSignature);
    } catch (err) {
        console.log(err);
    }
};
```

## Testing our functions

Now that we’ve completed the required functions, let’s test them out by creating a driver function! Create an empty function called `driverFunction`.

```
const driverFunction = async () => {
}
``` 

Now, add the following three lines to it.
```
await getWalletBalance();
await airDropSol();
await getWalletBalance();
```

Basically, we’re first checking the balance of our wallet, airdropping 5 SOL to it and then checking the balance again to confirm that the airdrop was successful. 
When you combine all the functions, your `index.md` must look like below
```
const {
  Connection,
  PublicKey,
  clusterApiUrl,
  Keypair,
  LAMPORTS_PER_SOL,
  Transaction,
  Account,
} = require("@solana/web3.js");

//STEP-1 Generating a new wallet keypair
const newPair = new Keypair();
console.log(newPair);

//STEP-2 Storing the public and private key
const publicKey = new PublicKey(newPair._keypair.publicKey).toString();
const secretKey = newPair._keypair.secretKey;

//STEP-3 Getting the wallet Balance
const getWalletBalance = async () => {
  try {
    const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
    const myWallet = await Keypair.fromSecretKey(secretKey);
    const walletBalance = await connection.getBalance(
      new PublicKey(myWallet.publicKey)
    );
    console.log(`=> For wallet address ${publicKey}`);
    console.log(`   Wallet balance: ${parseInt(walletBalance)/LAMPORTS_PER_SOL}SOL`);
  } catch (err) {
    console.log(err);
  }
};

//STEP-4 Air dropping SOL (in terms of LAMPORTS)
const airDropSol = async () => {
  try {
    const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
    const walletKeyPair = await Keypair.fromSecretKey(secretKey);
    console.log(`-- Airdropping 2 SOL --`)
    const fromAirDropSignature = await connection.requestAirdrop(
      new PublicKey(walletKeyPair.publicKey),
      2 * LAMPORTS_PER_SOL
    );
    await connection.confirmTransaction(fromAirDropSignature);
  } catch (err) {
    console.log(err);
  }
};

//STEP-5 Driver function
const driverFunction = async () => {
    await getWalletBalance();
    await airDropSol();
    await getWalletBalance();
}
driverFunction();
```
You can run index.md using the following command.
```
node index.js
```

If everything works fine, your output should look like 
![1](https://github.com/altsam/create_an_airdrop_program/raw/main/learn_assets/1.png)

## Conclusion

Congratulations on completing the Solana 101 airdrop program quest! With the ever growing Solana development ecosystem, you’re in for a wild and exciting ride. In the upcoming quests, we’ll be dipping our toes in fun projects like creating your very own cryptocurrency, or writing smart contracts for Solana in Rust. **Meanwhile, you can proceed to [Layer3](https://alpha.layer3.xyz/task/create-an-airdrop-program-with-solana-web3-js) and claim your NFT reward for succesfully completing this quest!**
