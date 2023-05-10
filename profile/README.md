# Solana MobileConnect

## The goal: Users should always have the option to use their mobile wallet -- no matter where their dApp is running

For example, it should be possible to run a dApp on a desktop computer (even one that isn't yours) and still be able to safely interact with it.

For the user, this means that they can store their keys **safely** in their mobile wallet. It is also more **convenient** since it e.g. allows you to manage a dApp on a larger monitor.

## How does it work?

The core component of Solana MobileConnect is a **session server** that manages login and transaction sessions. It does most of the heavy lifting.

Here's the flow of a transaction initiated by the user:

![Transaction flow](/img/flow.svg)

### Step 1

The frontend serializes the transaction and sends it to the server to create a new transaction session. The transaction is now accessible via the unique ID of the session. On the frontend, this unique link is encoded in a QR code and displayed to the user. Once the user scans it, they will receive the transaction (using Solana Pay's Transaction Requests). They sign it and it is broadcast to the network.

### Step 2

The frontend polls the state of the transaction session, which prompts the server to check the state of the blockchain.

### Step 3

After a while, the transaction will have reached the `confirmed` state. This will be reflected in the state of the transaction session returned by the server. The frontend can now notify the user.

### Logging in

To log in, the frontend creates a new login session on the server.

Each login session is associated with a unique link, which is encoded in a QR code.

When the user scans it, their public key is sent to the server (according to Solana Pay's Transaction Requests), allowing the server to capture and associate it with the login session.

Meanwhile, the frontend polls the server for the login session and eventually receives the public key. This is when the user is logged in.

Note that, when the user scans the QR code, the server sends back a dummy transaction and ignores it afterwards -- since we're only interested in the public key.

### Wallet adapter

The role of the wallet adapter is to be an intermediary between frontend and session server. For example, when the frontend code makes a `sendTransaction` request, the wallet adapter creates a new transaction session on the server, displays the QR code to the user and handles the polling.

## Why this solution?

A browser extension would require the user to install yet another extension -- on every device and for a single use case. It's not an ideal user experience.

WalletConnect is a lot more complicated to set up for developers. It also uses websockets, which tend to be more fragile. It also takes some time to set up a persistent connection, while MobileConnect instantly generates a QR code.

With MobileConnect, we have a relatively simple setup for developers, a great UX (users can use it right out of the box without installing anything). This is mostly achieved by leveraging already existing technologies -- especially Solana Pay.

## How to add it to my dApp?

You first have to decide which session server you want to use.  If a public server is sufficient for your use case (e.g. for testing), you can skip this step. If you want to set up your own server, you can clone [this repository]() and set it up accordingly.

Now, you need to integrate MobileConnect's wallet adapter into your dApp.

Run `npm install solana-mobileconnect-wallet-adapter` to install the package.

You include it like any other wallet adapter:
```
import { MobileConnectWalletAdapter } from 'solana-mobileconnect-wallet-adapter'

const WalletContextProvider: FC<{ children: ReactNode }> = ({ children }) => {
    const wallets = [
      new MobileConnectWalletAdapter(),
    ]
}
```

If you have set up a private server, you must also pass its URL like this: `new MobileConnectWalletAdapter("your-server.com")`

## Links

[Project's website](https://solana-mobileconnect)

[NFT Demo](https://solana-mobileconnect/nft-demo)
