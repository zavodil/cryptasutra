---
description: Создание Staking в BetaNet
---

# Запуск стейкинга в Near Protocol



{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардином на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

### _Перед началом_

Запустите ноду Nearup \([инструкция по установке](https://nodes.cryptasutra.com/near-protocol/nearup)\).

Убедитесь, что нода Nearup успешно и полностью сихнонизирована и только потом открывайте стейкинг. В режиме неполной синхронизации нода не в состоянии создавать и вализировать блоки, так что если вдруг сеть выберет вас вализатором, а ваша нода окажется не в стостоянии выполнять эту роль, то это может повлечь за собой потерю награды и исключение из эпохи.

Стейкинг отключен в сети _TestNet_ и доступен только в _BetaNet_. Поэтому обязательно укажите в NEAR Shell, что работаете в BetaNet, это можно сделать командой `export NODE_ENV=betanet` . Сервисы для работы в каждой сети:

{% tabs %}
{% tab title="⛔️ TestNet" %}
Блок-эксплорер [https://explorer.testnet.nearprotocol.com/](https://explorer.testnet.nearprotocol.com/)

Кошелек [https://wallet.testnet.nearprotocol.com/](https://wallet.testnet.nearprotocol.com/)

Удалённый вызов процедур [https://rpc.testnet.nearprotocol.com/](https://rpc.testnet.nearprotocol.com/)
{% endtab %}

{% tab title="✅ BetaNet" %}
Блок-эксплорер [https://explorer.betanet.nearprotocol.com/](https://explorer.betanet.nearprotocol.com/)

Кошелек [https://wallet.betanet.nearprotocol.com/](https://wallet.betanet.nearprotocol.com/)

Удалённый вызов процедур [https://rpc.betanet.nearprotocol.com/](https://rpc.betanet.nearprotocol.com/)
{% endtab %}

{% tab title="MainNet" %}
Блок-эксплорер [https://explorer.nearprotocol.com/](https://explorer.nearprotocol.com/)

Кошелек [https://wallet.nearprotocol.com/](https://wallet.nearprotocol.com/)

Удалённый вызов процедур [https://rpc.nearprotocol.com/](https://rpc.nearprotocol.com/)
{% endtab %}
{% endtabs %}



| ⛔️ TestNet | ✅ BetaNet | MainNet |
| :--- | :--- | :--- |
| [https://explorer.testnet.nearprotocol.com](https://explorer.testnet.nearprotocol.com/) | [https://explorer.betanet.nearprotocol.com](https://explorer.betanet.nearprotocol.com/) | [https:~~/~~/explorer.nearprotocol.com](https://explorer.nearprotocol.com/) |
| [https://wallet.testnet.nearprotocol.com](https://wallet.testnet.nearprotocol.com/) | [https://wallet.betanet.nearprotocol.com](https://wallet.betanet.nearprotocol.com/) | [https://wallet.nearprotocol.com](https://wallet.nearprotocol.com/) |
| [https://rpc.testnet.nearprotocol.com](https://rpc.testnet.nearprotocol.com/) | [https://rpc.betanet.nearprotocol.com](https://rpc.betanet.nearprotocol.com/) | [https://rpc.nearprotocol.com](https://rpc.nearprotocol.com/) |

Обратите внимание, что сеть BetaNet сбрасывается каждый вторник в 6pm Pacific \(среда 4:00 по Москве\), так что учитывайте необходимость обновления и перезапуска ноды.

## Требования к ноду

Чтобы стать валидатором, вам потребуется запустить ноду на компьютере или облачном хостинге, опеспечив минимально необходимые требования:

* Хотя бы 2 CPU 
* Хотя бы 4GB RAM 
* Хотя бы 50 GB свободного места на диске

## Setting up your environment

**IMPORTANT: Make sure you have the latest version of NEAR Shell and Node Version; 10.x**

If this is not the case, follow the setps below to set up your environment; don't worry this won't take long. To stake, make sure that you have

* an account with tokens on **BetaNet**. If you have not set up an account yet, please navigate to the following page, set it up and come right back: [Create Account](https://github.com/nearprotocol/docs/blob/master/docs/local-setup/create-account.md). Remember to use the [BetaNet wallet](https://wallet.betanet.nearprotocol.com/)!
* `near-shell`, our CLI tool, which which will require [node.js](https://nodejs.org/en/download/) and [npm](https://www.npmjs.com/get-npm). You can check whether you have node.js and npm already installed by

  1. Open your command line;
  2. Type in

  This should display your node version in the command line.

  This should display your npm version in the command line.

  Otherwise, go ahead and install it with the following links [node.js](https://nodejs.org/en/download/). Note that node usually installs npm automatically. However, if you miss npm, please install it [from here](https://www.npmjs.com/get-npm).

Once node and npm are installed, go ahead and download the Near Shell; type the following in your terminal:

```text
# Download Near Shell with npm:
npm i -g near-shell
```

Once Near Shell is installed, go ahead and run your node.

### Run a Node

Now that you have the Near Shell, we can set-up your node. Please follow [Nearup documentation](https://github.com/near/nearup).

**IMPORTANT you will need your account ID here, which is your username from the account that you created in the previous step.**

Please come back to this screen, once you have completed the previous steps.

When asked for the account ID, enter the username of the account you want to stake with. You will be returned a public key used for staking; this will look similar to:

```text
Stake for user 'thefutureisnear.test' with 'ed25519:97JLghrxUQMaX2pcerVB5FNFu4qk8rx8J3fnWRyoEB7M'
```

Make sure you copy this validator\_key as you will need it for the next step. You can also find this public key at the following path in your near files `~/.near/betanet/validator_key.json`

## Send a staking transaction

Awesome! Once you completed the previous steps, you are all set for staking.

First let's authenticate near shell by running the command `near login`

You will be asked to navigate to a url to authenticate your staking account.

```text
Please navigate to this url and follow the instructions to log in:
https://wallet.betanet.nearprotocol.com/login/?title=NEAR+Shell&public_key=FSgxX7YwuCveCeYqsSAB3sD8dgdy3XBWztCQcEjimpaN
```

Once done, enter that account ID in the shell:

```text
Please enter the accountId that you logged in with:
```

When you have entered your account ID, it will display the following message:

`Missing public key for in default` `Logged in with masternode24`

This message is not an error, it just means that it will create a public key for you.

Now you're ready to send a staking transaction.

```text
near stake <accountId> <staking public key> <amount to stake>
```

Staking 50,000 NEAR should be enough on BetaNet.

You should see a success message that looks something like:

```text
Staking 50000 on thefutureisnear with public key = A4inyaard6yzt1HQL8u5BYdWhWQgCB87RbRRHKPepfrn.
```

## Being chosen to become a validator

After this, you will need to wait the ~6 hours bonding period on BetaNet to see if you have staked enough to become a validator. You can see you are a validator when in the logs of the node you see "V/" - where V means this node is currently a validator.

Legend: \# 7153 \| BlockHeight V/1 \| 'V' \(validator\) or '—' \(regular node\)

The 0/0/40 shows the total validators: connected peers / up to date peers / my peers. This number may change over time.

To learn more about how validators are chosen, take a look at the [Validator FAQ](https://github.com/nearprotocol/docs/blob/master/docs/validator/validator-faq.md).

## See current list of Validators and stake amounts

To see the current list of validators, you can take a look here: [http://rpc.betanet.nearprotocol.com/status](http://rpc.betanet.nearprotocol.com/status)

If you would like to see how much a validator is staking, you can run the command `near state`in Near Shell.

```text
{
  amount: '100011163887239132720351',
  code_hash: '11111111111111111111111111111111',
  locked: '97985903901882082761',
  storage_paid_at: 25,
  storage_usage: 182
}
```

## Automatically re-staking

NEAR Protocol automatically re-stake your rewards, unless you decide to unlock the funds. Issue the command `near stake` again, with a lower value, and your funds will be unlocked within three epochs \(~9 hours on BetaNet, ~36 hours on TestNet\).

