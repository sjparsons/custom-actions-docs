[&lt; Home](../README.md)

# Braintree CLI

The Braintree CLI allows developers to interact with Braintree services directly from their machine and leverage integrated development tools to build unique payment experiences.

> **Note:** You can learn how to use the CLI to build a Custom Action in our [guides](../guides/accept-a-new-payment-method.md).

<details>
<summary><strong>Contents</strong></summary>

- [Braintree CLI](#braintree-cli)
  - [Requirements](#requirements)
  - [Installation](#installation)
    - [via `npm`](#via-npm)
  - [Commands](#commands)
    - [Actions](#actions)
  - [Updating the CLI](#updating-the-cli)
    </details>

## Requirements

- Node.js v10.16 or later ([download](https://nodejs.org/en/download/))
  - [nvm](https://github.com/nvm-sh/nvm) is recommended to manage Node.js versions.
- Access to Braintree's Github Enterprise

## Installation

### via `npm`

> :warning: You will need access to the CLI Github repo to use Custom Actions
>
> Email functions-requests@braintreepayments.com to learn more.

```sh
$ npm i -g git+ssh://git@github.braintreeps.com:braintree/cli
```

Run `btx` from the command-line to verify your installation.

## Commands

### Actions

The `actions` namespace is used for managing Custom Actions integrations. A detailed reference can be found in the [CLI repo](https://github.braintreeps.com/braintree/cli) (GHE link).

## Updating the CLI

To update the CLI, re-run the [installation command above](#via-npm).
