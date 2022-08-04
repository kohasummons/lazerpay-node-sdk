<div align="center">
<img title="Lazerpay" align="center" src= "https://res.cloudinary.com/njokuscript/image/upload/v1646279538/lazerpay_logo_no-bg_trkkye.png" width="300px"/>
</div>
  
#  Lazerpay Nodejs SDK

[![npm version](https://img.shields.io/npm/v/lazerpay-node-sdk)](https://www.npmjs.org/package/lazerpay-node-sdk)

<!-- <div> -->
<!-- <img title="Lazerpay" src= "https://res.cloudinary.com/njokuscript/image/upload/v1646279538/lazerpay_logo_no-bg_trkkye.png" width="300px"/>  -->
<!-- [![npm version](https://img.shields.io/npm/v/lazerpay.svg?style=flat-square)](https://www.npmjs.org/package/lazerpay-node-sdk) -->

<!-- TODO -->
<!-- Submit Lazerpay logo to simple icons -->
<!-- </div> -->

The Lazerpay Nodejs SDK provides easy access to the Lazerpay API for applications written in server-side javascript.

## Requirements
<!-- TO REVIEW -->
Node 8, 10 or higher

## Installation

Using npm:

```bash
# install locally(recommended)
npm install lazerpay-node-sdk --save
```

Using yarn

```bash
yarn add lazerpay-node-sdk
```

## Usage

The package needs to be configured with your account's secret key and public key, which is
available in the [Lazerpay Dashboard][api-keys]. Require Lazerpay from the `lazerpay-node-sdk` and instantiate it with your key's value:

> Use TEST API keys for testing, and LIVE API keys for production.

> Never expose your secret keys. We recommend you supply your keys from a .ENV file.

```js
const Lazerpay = require('lazerpay-node-sdk');
const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

lazer.Misc.getWalletBalance("USDT")
  .then(walletBalance => console.log(walletBalance))
  .catch(error => console.error(error));
```

Using ES modules and `async`/`await`:

```js

import Lazerpay from 'lazerpay-node-sdk';
const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

(async () => {
  try{
    const walletBalance = await lazer.Misc.getWalletBalance("USDT");
    console.log(walletBalance);
  } catch(error){
      console.log(error)
  }
})();

```

## Documentation

The SDK exposes the following methods:

**1**. **Payment**

- [Initialize Payment](#initialize-payment) - initiate a crypto payment transfer
- [Confirm Payment](#initialize-payment) - confirm your customers transaction after payment has been made

**2**. **Payout**

- [Crypto Payout](#initialize-payment) - withdraw crypto in Lazerpay balance to an external address
- Bank Payout - This is coming to v2

**3**. **Swap**

- [Crypto swap](#crypto-swap) - swap between two stable coins
- [Get Crypto Swap Amount Out](#get-crypto-swap-amount-out) - get amount to receive before swaping coins

**4**. **Payment Links**

- [Create payment links](#create-a-payment-link) - create a Payment link
- [Get all payment links](#get-all-payment-links) - get all Payment links created
- [Get a single payment link](#get-a-single-payment-link) - get a Payment link by it's identifier
- [Update a payment Link](#update-a-payment-link) - disable or enable a Payment link

**5**. **Misc**

- [Get all accepted coins](#get-all-payment-links) - get the list of accepted cryptocurrencies on Lazerpay
- [Get wallet balance](#get-wallet-balance) - get wallet balance

## Payment

### `Initialize Payment`

This describes to allow your customers initiate a crypto payment transfer.

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const paymentTxn = async () => {
  try {
    const transaction_payload = {
      reference: 'YOUR_REFERENCE', // Replace with a reference you generated
      customer_name: 'Njoku Emmanuel',
      customer_email: 'kalunjoku123@gmail.com',
      coin: 'BUSD', // BUSD, DAI, USDC or USDT
      currency: 'USD', // NGN, AED, GBP, EUR
      amount: 100,
      accept_partial_payment: true, // By default it's false
      metadata: { 
        type: "Wallet fund"
      } // Metadata is an optional param
    };

    const response = await lazer.Payment.initializePayment(transaction_payload);

    console.log(response);
  } catch (error) {
    console.log(error);
  }
};
```

| Payload              | Default            | Description                                                                                                                                                                                                                                       |
| ------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `reference`        | `null`             | Reference your generated
| `customer_name`        | `null`             | Name of the customer
| `customer_email`        | `null`             | Email address of the customer
| `coin`        | `BUSD`             | -
| `currency`        | `USD`             | -
| `amount`        | `null`             | Amount to recieve from customer
| `accept_partial_payment`        | `false`             | Allow partial payment from custormer. [Learn more](https://--)
| `metadata`        | `null`             | Add additional information to the payment transaction. Metadata is an optional parameter. [Learn more](#https://--)

### `Confirm Payment`

This describes to allow you confirm your customers transaction after payment has been made.

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const confirm_tx = async () => {
  try {
    const payload = {
      identifier:
        'address generated or the reference generated by you from initializing payment',
    };

    const response = await lazer.Payment.confirmPayment(payload);

    console.log(response);
  } catch (error) {
    console.log(error);
  }
};
```

## Transfer

### `Crypto Payout`

This describes to allow you withdraw the crypto in their lazerpay balance to an external address

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const crypto_payout_tx = async () => {
  const transaction_payload = {
    amount: 1,
    recipient: '0x0B4d358D349809037003F96A3593ff9015E89efA', // address must be a bep20 address
    coin: 'BUSD',
    blockchain: 'Binance Smart Chain',
    metadata: {
      id: "343243243223432"
    } // Metadata is an optional param
  };
  try {
    const response = await lazer.Payout.transferCrypto(transaction_payload);
    console.log(response.error);
  } catch (e) {
    console.log(e);
  }
};
```

## Swap

### `Crypto swap`

This describes to allow you swap between two stable coins

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const crypto_swap_tx = async () => {
  const swap_payload = {
      amount: 100,
      fromCoin: 'BUSD',
      toCoin: 'USDT',
      blockchain: 'Binance Smart Chain',
      metadata: {
      id: "343243243223432"
    } // Metadata is an optional param
  };
  try {
    const response = await lazer.Swap.cryptoSwap(swap_payload);
    console.log(response.error);
  } catch (e) {
    console.log(e);
  }
};
```

### `Get Crypto Swap Amount Out`

This describes the amount you will receive on swap even before initiating the swap  

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const crypto_swap_tx = async () => {
  const swap_payload = {
      amount: 100,
      fromCoin: 'BUSD',
      toCoin: 'USDT',
      blockchain: 'Binance Smart Chain',
  };
  try {
    const response = await lazer.Swap.getCryptoSwapAmountOut(swap_payload);
    console.log(response.error);
  } catch (e) {
    console.log(e);
  }
};
```

## Payment Links

### `Create a payment link`

This describes creating a Payment link programatically

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const create_paymentlink_tx = async () => {
  const transaction_payload = {
    title: 'Njoku Test',
    description: 'Testing this sdk',
    logo:
      'https://assets.audiomack.com/fireboydml/bbbd8710eff038d4f603cc39ec94a6a6c2c5b6f4100b28d62557d10d87246f27.jpeg?width=340&height=340&max=true',
    currency: 'USD',
    type: 'standard',
    amount: 100 // Optional
  };
  try {
    const response = await lazer.PaymentLinks.createPaymentLink(
      transaction_payload
    );
    console.log(response);
  } catch (e) {
    console.log(e);
  }
};
```

### `Update a payment link`

This describes disabling or enabling a payment link by updating it

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const transaction_payload = {
  identifier: '7f2vrd8n',
  status: 'inactive', // status should either be active or inactive
};

const update_paymentLink = async () => {
  try {
    const response = await lazer.PaymentLinks.updatePaymentLink(
      transaction_payload
    );
    console.log(response);
  } catch (e) {
    console.log(e);
  }
};
```

### `Get all payment links`

This describes to allow you get all Payment links created

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const get_all_paymentlinks = async () => {
  try {
    const response = await lazer.PaymentLinks.getAllPaymentLinks();
    console.log(response);
  } catch (e) {
    console.log(e);
  }
};
```

### `Get a single payment link`

This describes to allow you get a Payment link by it's identifier

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const identifier = '7f2vrd8n';

const get_paymentlink = async () => {
  try {
    const response = await lazer.PaymentLinks.getPaymentLink(identifier);
    console.log(response);
  } catch (e) {
    console.log(e);
  }
};
```

## Misc

### `Get Accepted Coins`

This gets the list of accepted cryptocurrencies on Lazerpay

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const get_accepted_coins = async () => {
  try {
    const response = await lazer.Misc.getAcceptedCoins();
    console.log(response);
  } catch (error) {
    console.log(error);
  }
};
```

### `Get Wallet Balance`

Get wallet balance by specifying the coin

```javascript
const Lazerpay = require('lazerpay-node-sdk');

const lazer = new Lazerpay(LAZER_PUBLIC_KEY, LAZER_SECRET_KEY);

const get_wallet_balance = async () => {
  try {
    const coin = "USDT" // BUSD, DAI, USDC or USDT
    const response = await lazer.Misc.getWalletBalance(coin);
    console.log(response);
  } catch (error) {
    console.log(error);
  }
};
```

[api-keys]: https://dashboard.lazerpay.finance/settings
