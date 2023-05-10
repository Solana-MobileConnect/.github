# Solana MobileConnect

## What is the goal?

Users should always have the option to use their mobile wallet -- no matter where their dApp is running.

For example, it should be possible to run a dApp on a desktop computer (even one that isn't yours) and still be able to safely interact with it.

For the user, this means that they can store their keys safely in their mobile wallet. It is more convenient since it e.g. allows you to manage a dApp on a larger monitor.

## How does it work?

The core component of Solana MobileConnect is a session server that manages login and transaction sessions. It does most of the heavy lifting.

Here's the flow of a transaction initiated by the user:

![Transaction flow](/img/flow.svg)

### Step 1

The frontend serializes the transaction and sends it to the server to create a new transaction session. The transaction is now accessible via the unique ID of the session (using Solana Pay's Transaction Requests). On the frontend, this unique link is encoded in a QR code and displayed to the user. Once the user scans it, they will receive the transaction. They sign it and it is broadcast.

### Step 2

The frontend polls the state of the transaction session, which prompts the server to check the state of the blockchain.

### Step 3

After a while, the transaction will have reached the `confirmed` state. This will be reflected in the state of the transaction session. The frontend can now notify the user.

### Logging in

To log in, a new login session is created on the server. Each login session is associated with a unique link. This link is encoded in a QR code. When the user scans it, their public key is sent to the server, allowing us to capture it.

In this case, the server sends back a dummy transaction, but ignores it afterwards -- since we're only interested in the public key.

The frontend polls the server about the login session and eventually receives the public key. This is when the user is logged in.

### Wallet adapter

The role of the wallet adapter is to be an intermediary between frontend and session server. For example, when the frontend code makes a `sendTransaction` request, the wallet adapter creates a new transaction session on the server, displays the QR code to the user and handles the polling.

## Why this solution?

A browser extension would require the user to install yet another extension. It's not an ideal user experience.

WalletConnect is a lot more complicated, thus harder to set up. It also uses websockets, which tend to be more fragile.

Here, we have a relatively simple setup for developers, a great UX (they can use it right out of the box). This is achieved by leveraging existing technologies (especially Solana Pay).

## How to add it to my dApp?

You first have to decide which session server you want to use.

In case a public server is sufficient (e.g. for testing), you are done.

If you want to set up your own server, you can use [this repository]().

In the second step, you integrate MobileConnect's wallet adapter into your dApp.

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

If you have set up a private server, you must pass its URL like this: `new MobileConnectWalletAdapter(your-server.com)`

## Links

[The project's website](https://solana-mobileconnect).
