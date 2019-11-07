# Custom Actions

<sup>**Last edit:** 9/12/2019</sup>

# :warning: WIP DOCS

> Availability: We're building Custom Actions with the same proven expertise and rock-solid foundations you expect from Braintree.
>
> Email functions-requests@braintreepayments.com to learn more.

Custom Actions is a serverless platform that opens the Braintree ecosystem to you and your developers by providing the capability to extend our built-in offerings. With Custom Actions, you can add new payment methods we don't currently support, send transaction data to your accounting systems, run fraud tools from the vendor of your choice, or develop a custom workflow unique to your business.

<details>
<summary><strong>Contents</strong></summary>

- [Custom Actions](#custom-actions)
  - [What is it?](#what-is-it)
  - [Benefits](#benefits)
    - [Single integration](#single-integration)
    - [Unified reporting](#unified-reporting)
  - [Use cases](#use-cases)
    - [Payment methods](#payment-methods)
    - [Fraud tools](#fraud-tools)
    - [Data export](#data-export)
  - [Getting Started](#getting-started)
    - [Requirements](#requirements)
    - [Create a new project](#create-a-new-project)
  - [Guides](#guides)
  - [Reference](./reference.md)
    </details>

## What is it?

We've defined **triggers** within Braintree that can execute code you've deployed into our platform. This code, called a **Custom Action** behaves as an **Event Handler** and is responsible for interacting with partners and vendors connected with your business. Often these handlers are simply mapping data from a third-party source to Braintree specific data.

Custom Actions focuses on developer experience as a first-class value. We provide tools to allow you to iterate quickly and deploy seamlessly. At its core, Custom Actions uses GraphQL and TypeScript to ensure type safety for your integration. These technologies also allow you to develop your integration locally within your own environment and leverage familiar tooling.

## Benefits

### Single integration

Payment methods run through Custom Actions are actionable within Braintree, just like any other. For example, you can initiate refunds or settle transactions the same as you would for a card payment.

### Unified reporting

By consolidating all of your transaction data within Braintree, you can view all of your commerce data in one place.
Globally available
Custom Actions are deployed globally. This means you can tailor your commerce to specific regions and enhance the experience for your users by minimizing latency.

## Use cases

### Payment methods

With Custom Actions, you can accept a payment method Braintree doesn’t support or initiate complex workflows with third-parties such as Twilio to take payment information over the phone.

[IMAGE]

### Fraud tools

You can use Custom Actions to run transactions through a fraud service.

[IMAGE]

### Data export

_Coming soon…_

## Getting Started

- [Getting Started](#getting-started)
  - [Requirements](#requirements)
  - [Create a new project](#create-a-new-project)

### Requirements

- A Braintree sandbox account – ([signup here](https://www.braintreepayments.com/sandbox))
- Node.js v10.15 or later ([download](https://nodejs.org/en/download/))
  - [nvm](https://github.com/nvm-sh/nvm) is recommended to manage Node.js versions.
- Familiarity with [git](https://git-scm.com/) and command-line tools

### Create a new project

Clone the [Payment Method](https://github.com/braintree/custom-actions-payment-method) starter repo and follow the README for setup instructions. In this repo, you will find **Event Handlers** for various payment method hooks as well as a configuration file describing your integration.

Make sure to change the `name` to reflect your new Custom Action.

<sub style="margin-bottom: -10px; display: block;"><strong>braintree.yml</strong></sub>

```yml
# Add your project name here
name: custom-actions-payment-method

kind: paymentMethod

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

This project provides TypeScript definitions and unit tests out of the box. After installing dependencies, you can run `npm run test:watch` to spin up the tests in watch mode and begin wiring up your custom integration.

## Guides

- [Accept a new payment method](./accept-a-new-payment-method.md)
