# Solana MobileConnect

The goal of this project is to allow users to always use their mobile wallet -- no matter where the target dApp is running.

The key difference to Solana Pay is that you can log into an app before making a transaction. This means that the app knows who you are and can show you a personalized interface.

## How does it work?

Here's the flow for a transaction initiated by the user:

![Transaction flow](/img/flow.svg)

To send a transaction, the dApp passes it to MobileConnect's wallet adapter. It serializes it and sends it to a server to be stored there. It also returns a link to be encoded in the QR code. When the user scans it, the 

It builds on Solana Pay's Transaction Requests.

To log in, a new login session is created on a server and the user's request is directed there. This way, we can get their public key.

The login process works similarly. Only that in this case, the server sends a dummy transaction to the phone, which is ignored afterwards. Only the user's public key is relevant in this case. It is stored on the server until it's retrieved by the frontend through polling. This is when the user logs in.

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
