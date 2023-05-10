# Solana MobileConnect

## What is the goal?

Users should have the option to use their mobile wallet -- no matter where the dApp is running that they want to interact with.

In particular, it should be possible to run a dApp on a desktop computer (even one that isn't yours) and still be able to safely interact with it.

## How does it work?

The key component is a session server that runs in the background and manages login and transaction servers. It does the heavy lifting.

Here's the flow of a transaction initiated by the user:

![Transaction flow](/img/flow.svg)

Step 1: The frontend serializes the transaction and sends it to the server to create a new transaction session. The transaction is now accessible via the unique ID of the session (using Solana Pay's Transaction Requests). On the frontend, this unique link is encoded in a QR code and displayed to the user. Once the user scans it, they will receive the transaction. They sign it and it is broadcast.

Step 2: The frontend polls the state of the transaction session, which induces the server to check the state of the blockchain.

Step 3: At a certain point, the transaction will be confirmed. This is reflected in the transaction session. The frontend now has the tx signature and can notify the user accordingly.

### Logging in

To log in, a new login session is created on a server. Each login session is associated with a unique link. This link is encoded in a QR code. When the user scans it, their public key is sent to the server, allowing us to capture it.

In this case, the server sends back a dummy transaction, but ignores it afterwards -- since we're only interested in the public key.

The frontend polls the server about the login session and eventually receives the public key. This is when the user is logged in.

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
