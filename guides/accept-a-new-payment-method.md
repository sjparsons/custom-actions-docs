[&lt; Home](./README.md)

# Accept a new payment method

> Custom Actions provides pre-defined project templates. Not every integration will fall neatly within a single template, but for those, you can create a custom integration or reach out to functions-requests@braintreepayments.com

By default, Braintree provides **hooks** for the [authorize](#authorization), [capture](#capture), [void](#void), and [refund](#refund) phases of a transaction. Learn more about the lifecycle of a Transaction [here](https://developers.braintreepayments.com/guides/transactions/node).

<details>
<summary><strong>Contents</strong></summary>

- [Requirements](#requirements)
  - [Project setup](#project-setup)
- [Event Handlers](#event-handlers)
  - [Tokenization](#tokenization)
  - [Authorization](#authorization)
  - [Capture](#capture)
  - [Reversals](#reversals)
    - [Void Handler](#void-handler)
    - [Refund Handler](#refund-handler)
      </details>

## Requirements

In addition to a [Braintree sandbox](https://www.braintreepayments.com/sandbox) account, familiarize yourself with our [GraphQL API](https://graphql.braintreepayments.com).

### Project setup

> :warning: **Custom Actions** is currently in preview. Reach out to functions-requests@braintreepayments.com to request access

Clone the [Payment Method](https://github.com/braintree/custom-actions-payment-method) starter repo and follow the README for setup instructions.

## Event Handlers

### Tokenization

Custom Actions works with existing Braintree integrations. For most payment methods, a collection of values is [tokenized](https://developers.braintreepayments.com/guides/payment-method-nonces) for use as a [Payment Method Nonce](https://developers.braintreepayments.com/guides/payment-method-nonces). For first-class payment methods, Braintree SDKs handle this tokenization for you. In the case of Custom Actions, you may tokenize the values specific to your integration and exchange them for a nonce.

With Custom Actions you can tokenize a collection of fields and data in exchange for a nonce and then use the decoded fields in subsequent Event Handlers. See the [Tokenization reference](./reference.md#tokenization) for details.

From your client, you can tokenize your values using the [Braintree GraphQL API](https://graphql.braintreepayments.com) via the `TokenizeCustomActionsPaymentMethod` mutation.

**GraphQL mutation**

```graphql
mutation TokenizeCustomActionsPaymentMethod(
  $input: TokenizeCustomActionsPaymentMethodInput!
) {
  tokenizeCustomActionsPaymentMethod(input: $input) {
    paymentMethod {
      details {
        ... on CustomActionsPaymentMethodDetails {
          actionName
          fields {
            name
            displayValue
          }
        }
      }
    }
  }
}
```

**variables**

```json
{
  "input": {
    "customActionsPaymentMethod": {
      "actionName": "FooPay",
      "fields": [
        {
          "name": "accountNumber",
          "value": "12345",
          "displayValue": "****5"
        },
        {
          "name": "accountName",
          "value": "Brian Tree",
          "displayValue": "Brian Tree"
        }
      ]
    }
  }
}
```

**response**

```json
{
  "data": {
    "tokenizeCustomActionsPaymentMethod": {
      "paymentMethod": {
        "id": "tokencap_123",
        "details": {
          "actionName": "FooPay",
          "fields": [
            {
              "name": "accountNumber",
              "displayValue": "****5"
            },
            {
              "name": "accountName",
              "displayValue": "Brian Tree"
            }
          ]
        }
      }
    }
  },
  "extensions": {
    "requestId": "some-unique-string"
  }
}
```

### Authorization

When you [authorize](https://graphql.braintreepayments.com/guides/creating_transactions#using-separate-authorization-and-capture) a payment method via the [Braintree API](https://graphql.braintreepayments.com) using a [tokenized Custom Actions payment method](#tokenization), your `authorizeTransaction` event handler will be called. This handler will provide you with a [Transaction](./reference.md) Object and expect you to return a [TransactionStatusEvent](./reference) with a new [status](TODO).

```js
export const AuthorizeTransactionHandler = async transaction => {
  // Call out to third-party here

  return {
    transactionStatusEvent: {
      id: transaction.id,
      status: BraintreeTransactionStatus.AUTHORIZED
    }
  };
};
```

---

**from your server**

```graphql
mutation ExampleAuth($input: AuthorizePaymentMethodInput!) {
  authorizePaymentMethod(input: $input) {
    transaction {
      id
      status
    }
  }
}
```

**variables**

```json
{
  "input": {
    "paymentMethodId": "tokencap_123",
    "transaction": {
      "amount": "11.23"
    }
  }
}
```

**response**

```json
{
  "data": {
    "chargePaymentMethod": {
      "transaction": {
        "id": "id_of_authorized_transaction",
        "status": "AUTHORIZED"
      }
    }
  },
  "extensions": {
    "requestId": "some-unique-string"
  }
}
```

### Capture

Capturing a transaction represents the [settlement](https://developers.braintreepayments.com/guides/transactions/node#settlement) phase which is required to actually collect payment from a previous authorization.

When calling [`captureCharge`](https://graphql.braintreepayments.com/guides/creating_transactions#using-separate-authorization-and-capture) or [`chargePaymentMethod`](https://graphql.braintreepayments.com/guides/creating_transactions#charging-a-payment-method), your `captureTransaction` event handler will be called. Like the authorize handler, this function will be called with a [Transaction](./reference.md) Object and expect you to return a [TransactionStatusEvent](./reference) with a new [status](TODO).

Note: when using `chargePaymentMethod` your `authorizeTransactionHandler` _and_ your `captureTransactionHandler` will be invoked.

If your payment method supports inline capture and does not need to be batched, you an return a `SETTLED` or `SETTLEMENT_PENDING` event. If your payment method needs to be batched at set intervals, use the `SUBMITTED_FOR_SETTLEMENT` event with a `settlementTimestamp`.

```js
export const CaptureTransactionHandler = async transaction => {
  // Call out to third-party here

  return {
    transactionStatusEvent: {
      id: transaction.id,
      status: BraintreeTransactionStatus.SUBMITTED_FOR_SETTLEMENT,
      settlementTimestamp: new Date().toISOString()
    }
  };
};
```

---

**from your server**

```graphql
mutation ExampleCapture($input: CaptureTransactionInput!) {
  captureTransaction(input: $input) {
    transaction {
      id
      status
    }
  }
}
```

**variables**

```json
{
  "input": {
    "transactionId": "id_of_authorized_transaction"
  }
}
```

**response**

```json
{
  "data": {
    "transaction": {
      "id": "id_of_transaction",
      "status": "SUBMITTED_FOR_SETTLEMENT"
    }
  }
}
```

### Reversals

A transaction can be reversed by either _voiding_ the transaction if it has not be settled or _refunding_ the transaction if it has been settled. Braintree's API exposes a [`reverseTransaction`](https://graphql.braintreepayments.com/guides/reversing_transactions/) mutation to handle the logic automatically depending on the state of the transaction. Depending on the state, one of your handlers will be invoked, either `VoidTransactionHandler` or `RefundTransactionHandler`

**from your server**

```graphql
mutation ExampleReverse($input: ReverseTransactionInput!) {
  reverseTransaction(input: $input) {
    reversal {
      ... on Transaction {
        id
        status
        statusHistory {
          status
          terminal
        }
      }
      ... on Refund {
        id
        amount {
          value
        }
        orderId
        status
        refundedTransaction {
          id
          amount {
            value
          }
          orderId
          status
        }
      }
    }
  }
}
```

**variables**

```json
{
  "input": {
    "transactionId": "id_of_successful_transaction"
  }
}
```

**response**

```json
"data": {
  "reverseTransaction": {
    "reversal": {
      "id": "TRANSACTION_ID",
      "status": "VOIDED",
      "statusHistory": [
        {
          "status": "VOIDED",
          "terminal": true,
        },
        {
          "status": "SUBMITTED_FOR_SETTLEMENT",
          "terminal": false,
        },
        {
          "status": "AUTHORIZED",
          "terminal": false,
        }
      ]
    }
  }
}
```

#### Void Handler

A transaction in the [`AUTHORIZED`](https://developers.braintreepayments.com/reference/general/statuses#authorized) or [`SUBMITTED_FOR_SETTLEMENT`](https://developers.braintreepayments.com/reference/general/statuses#submitted-for-settlement) state can be voided. This will invoke the `voidHandler` function for your Custom Action with a [Transaction](./reference.md) Object and expect you to return a [TransactionStatusEvent](./reference) with a new status of `VOIDED`.

```js
export const VoidTransactionHandler = async transaction => {
  // Call out to third-party here

  return {
    transactionStatusEvent: {
      id: transaction.id,
      status: BraintreeTransactionStatus.VOIDED
    }
  };
};
```

#### Refund Handler

A transaction in the [`SETTLED`](https://developers.braintreepayments.com/reference/general/statuses#settled) or [`SETTLING`](https://developers.braintreepayments.com/reference/general/statuses#settling) state can be refunded. This will invoke the `refundTransaction` function for your Custom Action with a [Refund](./reference.md) Object and expect you to return a [TransactionStatusEvent](./reference) with a new status of `SUBMITTED_FOR_SETTLEMENT`.

```js
export const RefundTransactionHandler = async refund => {
  // Call out to third-party here

  return {
    transactionStatusEvent: {
      id: transaction.id,
      status: BraintreeTransactionStatus.SUBMITTED_FOR_SETTLEMENT
    }
  };
};
```
