# PsiFi Backend

The API behind [psifi-frontend](https://github.com/AI-pro017/psifi-frontend), a crypto wallet where people pay each other by username.

When someone signs up, the server creates a new HD wallet from a 24 word seed phrase and derives addresses for Bitcoin, Solana, Ethereum, Avalanche and BNB Chain. The seed is stored encrypted with a key derived from the user's password, so it can only be unlocked at login. The server also keeps a record of payments between users so the app can show transaction history and a contact list.

This was built as a testnet demo. It's a nice reference for multi-chain key derivation, but it isn't hardened for real funds.

## Tech stack

- Node.js with Express (ES modules)
- MongoDB with Mongoose
- bip39 and hdkey for seed phrases and key derivation
- bitcoinjs-lib, @solana/web3.js and ethers for addresses and transactions
- bcrypt for passwords and JWT for sessions

## Running it locally

You'll need Node.js 18 or newer and MongoDB, either installed locally or on Atlas.

```bash
git clone https://github.com/AI-pro017/psifi-backend.git
cd psifi-backend
npm install
```

Create a `.env` file:

```env
PORT=5000
MONGO_URL=mongodb://localhost:27017/psifi
JWT_SECRET=change-me
PINATA_API_KEY=
NODE_URL=https://api.avax-test.network/ext/bc/C/rpc
```

`NODE_URL` is the EVM RPC used to read token transfers and defaults to the Avalanche Fuji testnet. `PINATA_API_KEY` is only needed for uploading profile pictures from the server.

Start the server with auto reload:

```bash
npm run dev
```

or without it:

```bash
npm start
```

The API is served under `/api`, so with the settings above the frontend should point at `http://localhost:5000/api`.

## Endpoints

All routes are under `/api`.

| Method | Path | Description |
| --- | --- | --- |
| POST | `/` | Create an account and its wallet |
| POST | `/auth` | Log in |
| POST | `/logout` | Log out |
| POST | `/checkExistUser` | Check whether a username is taken |
| POST | `/getAddress` | Look up a user's addresses by username |
| GET | `/getAllUserName` | List all usernames |
| POST | `/getPrivateKey` | Unlock the key for one chain during a send |
| POST | `/recordMessage` | Save a payment with its tx hash and amounts |
| POST | `/getMessage` | Sent and received payments for an address |
| POST | `/getContactList` | People you've paid or been paid by |

## Project structure

```
index.mjs          Starts the server and connects to MongoDB
app.mjs            Express setup and middleware
routes/            Route definitions
controllers/       User and payment logic
models/            Mongoose models for users and payments
middlewares/       Auth and async error handling
utils/             Wallet creation, key derivation per chain, JWT and Pinata helpers
```

There's a `vercel.json` in the repo as well if you want to deploy it as a serverless function.
