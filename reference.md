[&lt; Home](./README.md)

# Reference

- [Reference](#reference)
  - [Event Handlers](#event-handlers)
  - [Configuration](#configuration)
    - [Environment variables](#environment-variables)
    - [Secrets and keys](#secrets-and-keys)
  - [Tokenization](#tokenization)
  - [Types](#types)
  - [Repos](#repos)
    - [Templates](#templates)

## Event Handlers

_Coming soon_

## Configuration

_Coming soon_

### Environment variables

_Coming soon_

### Secrets and keys

_Coming soon_

## Tokenization

You can tokenize custom values via the [`TokenizeCustomActionsPaymentMethod` mutation](https://graphql.braintreepayments.com/reference#Mutation--tokenizeCustomActionsPaymentMethod) in the [GraphQL API](https://graphql.braintreepayments.com).

Each field adheres to the following schema:

| Key            | Type     | Required | Description                                                                                                                                                                                                 |
| -------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`         | `String` | `true`   | The name of the field you wish to tokenize. For example, `accountNumber`.                                                                                                                                   |
| `value`        | `String` | `true`   | The raw value of the field you wish to tokenize. For example, `1234-5678`.<br /><br />This value will be provided to your Event Handlers, but **not** to clients or visible in the Braintree control panel. |
| `displayValue` | `String` | `false`  | The masked value for the tokenized field. For example: `****-5678`<br /><br />**Note:** It is up to your client to provide the masking logic for this value.                                                |

### Examples

**Mutation:**

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

**Input variables:**

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

## Types

## Repos

### Templates

- [Payment Method](https://github.com/braintree/custom-actions-payment-method)
