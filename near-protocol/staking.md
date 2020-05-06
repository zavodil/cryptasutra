---
description: Создание Staking в BetaNet
---

# Запуск стейкинга в Near Protocol

{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардингом на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

{% hint style="info" %}
Запуск ноды Nearup - часть программы [StakeWars](https://nodes.cryptasutra.com/near-protocol/stakewars).
{% endhint %}

### _Перед началом_

Запустите ноду `nearup` \([инструкция по установке](https://nodes.cryptasutra.com/near-protocol/nearup)\).

Убедитесь, что нода `nearup` успешно и полностью сихнонизирована и только потом открывайте стейкинг. В режиме неполной синхронизации нода не в состоянии создавать и валидировать блоки, так что если вдруг сеть выберет вас валидатором и ваша нода окажется не в состоянии выполнять эту роль, то это может повлечь за собой потерю награды и исключение из эпохи. Прогресс синхронизации в процентах указывается при запуске ноды в логах/выводе.

Стейкинг отключен в сети `testnet` и доступен только в `betanet`. Поэтому обязательно укажите в NEAR Shell, что работаете в `betanet`, это можно сделать командой `export NODE_ENV=betanet` . Сервисы для работы в каждой сети:

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

Обратите внимание, что сеть `betanet` сбрасывается каждый вторник в 6pm Pacific \(среда 4:00 по Москве\), так что учитывайте необходимость обновления и перезапуска ноды.

## Требования к нодe

Чтобы стать валидатором, вам потребуется запустить ноду на компьютере или облачном хостинге, обеспечив минимально необходимые требования:

> * Хотя бы 2 CPU 
> * Хотя бы 4GB RAM 
> * Хотя бы 50 GB свободного места на диске

## Настройка окружения

**Внимание: Убедитесь, что у вас установлена новейшая версия NEAR Shell и NodeJS версии 10.x**

Если что-то из перечисленного еще не установлено, то не беда, мы сейчас это сделаем.

Итак, для открытия стейкинга потребуется:

* создать аккаунт с токенами на **`betanet`**. Если у вас такого нет, то [создайте аккаунт](https://github.com/nearprotocol/docs/blob/master/docs/local-setup/create-account.md). Используйте именно сеть [betanet](https://wallet.betanet.nearprotocol.com/)! Токены будут автоматически добавлены на аккаунт.
* запустить `near-shell`, это интерфейс с управлением через командную строку, для запуска которого потребуется [node.js](https://nodejs.org/en/download/) и [npm](https://www.npmjs.com/get-npm). Чтобы проверить, установлены ли они у вас:

  1. откройте терминал с командой строкой
  2. запустите 2 команды:

     ```text
     node -v 
     ```

     позволит проверить версию node.js. Требуется как минимум 10.14.2 версия.

     ```text
     npm -v
     ```

     выведет версия npm.



  Если чего-то будет недоставать, то вы увидите ошибку, в этом случае установите [node.js](https://nodejs.org/en/download/) \(это автоматически установит npm\). Отдельно npm можно установить [отсюда](https://www.npmjs.com/get-npm). После установки node.js еще раз проверьте версию, иногда можно по ошибке установить 8-ую версию. Для обновления до десятой запустите пару команд:

  ```text
  curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
  sudo apt install nodejs
  ```

### Запуск Near Shell

Когда и node.js, и npm установлены, пора скачать Near Shell, запустив команду в терминале

```text
# Скачать Near Shell с помощью npm:
npm i -g near-shell
```

Отлично, Near Shell запущен, теперь нужно залогиниться в него под нужным аккаунтом.

### Запуск ноды nearup

Если вы еще не установили ноду nearup, то воспользуйтесь [инструкцией по установке](https://nodes.cryptasutra.com/near-protocol/nearup).

**Внимание, далее вам потребуется ваш account ID, который является именем пользователя / адресом кошелька из предыдущего шага.**

Когда Near Shell запросит account ID, введите имя пользователя в аккаунте, подготовленном для стейкинга. 

{% hint style="info" %}
Вводите имя пользователя целиком, с указанием сети, например, `username.betanet`. Символ @ перед именем указывать не надо.
{% endhint %}

Вам будет возвращен публичный ключ следующего вида.

```text
Stake for user 'username.betanet' with 'ed25519:97JLghrxUQMaX2pcerVB5FNFu4qk8rx8J3fnWRyoEB7M'
```

Удостоверьтесь, что вы скопировали этот validator\_key, он нам скоро потребуется. В противном случае, его всегда можно найти в файле`~/.near/betanet/validator_key.json`

## Отправка транзакции на создание стейкинга

Отлично, больше нечего откладывать, всё готово для открытия стейкинга. Убедитесь, что вы работаете с сетью `betanet`, можно для гарантии еще раз ввести команду `export NODE_ENV=betanet`.

Давайте пройдем аутентификацию 

```text
near login
```

В ответ вам предложат перейти по url для входа в стейкинг-аккаунт. Откройте его в браузере, пройдите аутентификацию и следуйте инструкциям.

```text
Please navigate to this url and follow the instructions to log in:
https://wallet.betanet.nearprotocol.com/login/?title=NEAR+Shell&public_key=FSgxX7YwuCveCeYqsSAB3sD8dgdy3XBWztCQcEjimpaN
```

После этого укажите в Near Shell аккаунт, с которым вы прошли аутентификацию:

```text
Please enter the accountId that you logged in with:
```

Когда вы укажете имя аккаунта, может отобразиться следующее сообщение:

`Missing public key for in default` `Logged in with masternode24`

Это не ошибка, это значит, что сейчас Near Shell сейчас создаст для вас публичный ключ.

Теперь можно открыть стейкинг. Укажите accountId в полном виде \(например, `username.betanet`\), staking public key \(ключ validator\_key, который мы скопировали выше\), а также размер стейкинга в токенах NEAR.

```text
near stake <accountId> <staking public key> <amount to stake>
```

Стейкинг в размере около 50 000 токенов NEAR будет будет достаточным для работы валидатором в `betanet`.

В случае успеха, вы получите подобное сообщение:

```text
Staking 50000 on username.betanet with public key = A4inyaard6yzt1HQL8u5BYdWhWQgCB87RbRRHKPepfrn.
```

> Не кладите в стейкинг 100% вашего баланса, оставьте необходимое количество токенов для работы смарт-контракта и оплаты хранения данных аккаунта \(100 токенов $NEAR будет более чем достаточно\).

## Работа в роли валидатора

Через 6 часов устойчивой работы обладатели достаточного стейка могут оказаться в роли валидаторов. Чтобы убедиться в том, можно открыть логи и увидеть там статус  "V/" - где V будет означать статус валидатора. [Подробнее](https://nodes.cryptasutra.com/near-protocol/stakewars#proverka-rabotosposobnosti).

## Просмотр полного списка валидаторов и их стейков

Полный список нод-валидаторов `betanet` доступен по ссылке: [http://rpc.betanet.nearprotocol.com/status](http://rpc.betanet.nearprotocol.com/status)

Чтобы посмотреть размер стейка валидатора, можно отправить следующую команду  в Near Shell:

```text
near state
```

Примерно таким будет отклик сервера:

```text
{
  amount: '100011163887239132720351',
  code_hash: '11111111111111111111111111111111',
  locked: '97985903901882082761',
  storage_paid_at: 25,
  storage_usage: 182
}
```

## Автоматический рестейкинг

NEAR Protocol будет автоматически открывать рестейкинг для наград до того, как вы решите разблокировать ваши средства. Просто запустите еще раз команду `near stake` , указав значение меньше чем в предыдущий раз либо 0, и тогда ваши средства будут разблокированы в течение трех эпох \(~9 часов в `betanet`, ~36 часов в `testnet`\).

{% page-ref page="staking.md" %}

