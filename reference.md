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

Custom Actions projects are configured via a `braintree.yml` file. This file contains basic information about the structure of your project and allows you to set values such as environment variables.

```yml
# Add your project name here
name: custom-actions-payment-method

kind: paymentMethod

# Configure environment variables which will be provided at runtime
environment:
  sandbox:
    PUBLIC_KEY: "sandbox_key"
  production:
    PUBLIC_KEY: "prod_key"

functions:
  authorizeTransaction:
    handler: dist/index.AuthorizeTransactionHandler
  captureTransaction:
    handler: dist/index.CaptureTransactionHandler
  refundTransaction:
    handler: dist/index.RefundTransactionHandler
  voidTransaction:
    handler: dist/index.VoidTransactionHandler
```

### Environment variables

Environment variables can be set in the `braintree.yml` file as follows (you can see an [example here](https://github.com/braintree/custom-actions-payment-method/blob/546aa30843967129f20369faa5b1088b63550921/braintree.yml#L6-L8)):

```yml
environment:
  sandbox:
    PUBLIC_KEY: "sandbox_key"
  production:
    PUBLIC_KEY: "prod_key"
```

> Currently, variables must be configured explicitly for `sandbox` and `production`.

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
