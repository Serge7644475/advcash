[![Build Status](https://travis-ci.org/eriknakata/advcash.svg?branch=master)](https://travis-ci.org/eriknakata/advcash)
[![Code Climate](https://codeclimate.com/github/eriknakata/advcash/badges/gpa.svg)](https://codeclimate.com/github/eriknakata/advcash)
[![Test Coverage](https://codeclimate.com/github/eriknakata/advcash/badges/coverage.svg)](https://codeclimate.com/github/eriknakata/advcash/coverage)
[![dependencies Status](https://david-dm.org/eriknakata/advcash/status.svg)](https://david-dm.org/eriknakata/advcash)

# Advcash

node.js wrapper for [advcash](http://wallet.advcash.com/referral/d3bd61a9-5950-4d1b-8607-ec4c0f7a3576) cryptocurrency exchange

### Documentation

The official documentation can be found [here](https://advcash.com/files/documents/advcash.merchantapi-1.9_en.pdf)

### Prerequisites


- Node 6.0
- Advcash account ([click here](http://wallet.advcash.com/referral/d3bd61a9-5950-4d1b-8607-ec4c0f7a3576) to register)
- Advcash api key


### Installing

```
npm install --save advcash
```

## Examples

All methods returns a promise as result

### client

```js
var advcash = require('advcash');

var options = {
  password: 'password created previously',
  apiName: 'api created previously',
  email: 'email used to create the advcash account'
};

advcash(options).then(function(client) {
  // client is ready
})

```

### checkCurrencyExchange

Getting the currency exchange rate

#### Arguments

| Name     | Type    | Description                                                         |
|----------|---------|---------------------------------------------------------------------|
| from     | String  | [Transfer currencies](#transfer-currencies)                         |
| to       | String  | [Transfer currencies](#transfer-currencies)                         |
| action   | String  | BUY, SELL                                                           |
| amount   | Float   | Transaction amount (accuracy – up to two digits after decimal point)|

```js

var arguments = {
  from: "BTC",
  to: "USD",
  action: "SELL",
  amount: 0.5
};

client.checkCurrencyExchange(arguments).then(function(response) {
  console.log(response)
})

```

> Response

```json

{
  "amountExchanged": 636.32,
  "rate": 1272.63,
  "from": "BTC",
  "to": "USD",
  "action": "SELL",
  "amount": 0.5
}

```

### getBalances

Get Balance per User’s Wallets

```js

client.getBalances().then(function(balances) {
  console.log(balances)
})

```

> Response

```json

[
  {
    "amount": 0.55,
    "id": "U768564323906"
  },
  {
    "amount": 0.80,
    "id": "E527005319826"
  }
]

```

### validateAccount

Checking matching the  first and last name of the user in the Advanced Cash system with the name and last name in a third-party system

```js

var arguments = {
  email: "email@example.com",
  firstName: "First name example",
  lastName: "Last name example"
};

client.validateAccount(arguments).then(function(response) {
  console.log(response)
})

```

> Response

```json

{
  "firstNameMatchingPercentage": 90.55,
  "rate": 55.56
}

```


### validateAccounts

Validation of Account’s Existence

```js

var emails = ['email1@example.com', 'email2@example.com']

client.validateAccounts(emails).then(function(response) {
  console.log(response)
})

```

> Response

```json

[
  {
    "present": false,
    "accountEmail": "email1@example.com"
  },
  {
    "present": true,
    "accountEmail": "email2@example.com"
  }
]

```

### history

Transaction History

#### Arguments

| Name                | Type    | Description                                                         |
|---------------------|---------|---------------------------------------------------------------------|
| from                | Int     | Ordinal number of transaction to start displaying with              |
| count               | Int     | The number of transactions for disiplaying                          |
| sortOrder           | String  | ASC, DESC                                                           |
| startTimeFrom       | Date    | Start date for transactions to be selected                          |
| startTimeTo         | Date    | End date for transactions to be selected                            |
| transactionName     | String  | [Transaction Names](#transaction-names)                             |
| transactionStatus   | String  | [Transaction Statuses](#transaction-statuses)                       |
| walletId            | String  | Wallet                                                              |

```js

var arguments = {
  from: 1,
  count: 5,
  sortOrder: "ASC",
  startTimeFrom: new Date('2017-01-02'),
  startTimeTo: new Date(),
  transactionName: 'CURRENCY_EXCHANGE',
  transactionStatus: 'COMPLETED'
};

client.history(arguments).then(function(response) {
  console.log(response)
})

```

> Response

```json

{ 
  "id": "8d088e53-462c-4eb5-b596-70060db6b66d",
  "activityLevel": 0,
  "amount": 10.24,
  "comment": "",
  "currency": "EUR",
  "direction": "OUTGOING",
  "fullCommission": 0.00,
  "receiverEmail": "receiver@example.com",
  "sci": false,
  "senderEmail": "sender@example.com",
  "startTime": "2017-03-25T19:46:56.843Z",
  "status": "COMPLETED",
  "transactionName": "CURRENCY_EXCHANGE",
  "walletDestId": "U768564448973",
  "walletSrcId": "E5270053223408"
}

```

### validationSendMoney

Validation of Intrasystem Transfer

#### Arguments

| Name                 | Type    | Description                                                           |
|----------------------|---------|-----------------------------------------------------------------------|
| amount               | Float   | Transaction amount (accuracy – up to two digits after decimal point)  |
| currency             | String  | [Transfer currencies](#transfer-currencies)                           |
| email                | String  | Recipient’s email (Required if “walletId” is empty)                   |
| walletId             | String  | Recipient’s wallet (Required if “email” is empty)                     |
| note                 | String  | Note to transaction                                                   |
| savePaymentTemplate  | Boolean | Indicator of saving the current payment template                      |

If the validation of the expected payment is successful, the response from the server will contain a blank message. If the validation is not successful, a message with an error contained in its body will be returned.

```js

var arguments = {
  amount: 0.10,
  currency: "USD",
  email: "example@example.com",
  note: "testing",
  savePaymentTemplate: true
}

client.validationSendMoney(arguments).then(function(response) {
  console.log(response) // null
})
.catch(function(error) {
  console.log(error)
})

```

> Response

```json

null

```


### Transaction Statuses

| Value      | Description                        |
|------------|------------------------------------|
| PENDING    | Transaction processing is pending  |
| PROCESS    | Transaction is being processed     |
| COMPLETED  | Transaction is completed           |
| CANCELED   | Transaction is cancelled           |
| CONFIRMED  | Transaction is confirmed           |

### Transaction Names

| Value                       | Description                                |
|-----------------------------|--------------------------------------------|
| ALL                         | All transactions regardless of their type  |
| CHECK_DEPOSIT               | Funds deposit by bank check                |
| WIRE_TRANSFER_DEPOSIT       | Funds deposit from bank account            |
| WIRE_TRANSFER_WITHDRAW      | Funds withdrawal to bank account           |
| INNER_SYSTEM                | Intrasystem funds transfer                 |
| CURRENCY_EXCHANGE           | Currency exchange within account           |
| BANK_CARD_TRANSFER          | Funds withdrawal to external bank card     |
| ADVCASH_CARD_TRANSFER       | Funds transfer to Advanced Cash card       |
| EXTERNAL_SYSTEM_DEPOSIT     | Deposit funds through third-party system   |
| EXTERNAL_SYSTEM_WITHDRAWAL  | Withdrawal through third-party system      |
| REPAYMENT                   | Funds repayment                            |

### Transfer Currencies

| Value | Description        |
|-------|--------------------|
| USD   | US Dollar          |
| EUR   | Euro               |
| RUR   | Russian Rouble     |
| GBP   | Pound Sterling     |
| UAH   | Ukrainian Hryvnia  |
| BTC   | Bitcoin            |

## Contributing

- Erik Nakata
- Leonardo Cadastro

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
