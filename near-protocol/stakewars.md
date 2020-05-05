---
description: Программа вознаграждения за стейкинг и делегирование
---

# Создание ноды в NEAR для StakeWars 2.0

{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардином на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

### Stake Wars 2.0 

Это перезапуск программы делегирования токенов в сети NEAR следующими целями: 

* конечные пользователи смогут освоить принципы Proof of stake, делегирования токенов
* валидаторы протестируют платформу делегирования NEAR и начнут строить связи с пользователями
* разработчики NEAR Protocol получат обратную связь и зададут новый импульс развития нового типа DeFi продуктов и роста сообщества

### Как стать валидатором

* Заполните [специальную форму](https://forms.gle/5KabPsD4BefR6nv68), чтобы зарегистрироваться в программе Stake Wars 2.0 и получить токены для стекинга
* Создайте своей кошелек в сети betanet с использованием кошелька [hosted betanet wallet](https://wallet.betanet.nearprotocol.com/)
* Откройте и настройте Ubuntu/Debian VPS-хостинг согласно инструкции настройки [nearup](https://github.com/near/nearup) 
* Добавьте своего валидатора в [этот файл](https://github.com/nearprotocol/stakewars/blob/master/VALIDATORS.md) - отредактируйте и создайте запрос на pull request 

**Примечание:** Сеть Betanet сбрасывается каждый вторник в 6 pm Pacific \(среда 4:00 по Москве\), так что учитывайте необходимость обновления и перезапуска ноды.

## Инструкция для валидаторов

### 1.Создайте пару ключей

Установите [near-shell](https://github.com/nearprotocol/near-shell), приложение на Node.js, которое создает ключи доступа, подключается к платформе NEAR и отправляет транзакции по сети. Не обязательно устанавливаться на том же сервере, что и ноду, даже наоборот, лучше использовать другой компьютер.

1. Перейдите в рабочую директорию `cd`
2. Скачайте `near-shell` командой `npm install -g near-shell`
3. Запустите `npx create-near-app staking`
4. `cd` перейдите в директорию`staking`
5. Установите переменную окружения NODE\_ENV на betanet. Это можно сделать консольной командой`export NODE_ENV=betanet`
6. Запустите `near login` и следуйте инструкциям по подключению ноды
7. Сразу после подключения `near-shell` готов принимать команды по открытию стекинга

### 2. Получите токены

В разработке. [Используйте форму запроса токенов](https://docs.google.com/forms/d/1xarv54e-fFSuD2AQorAPx4086z3zyS5ZNGwcLr4QkeQ) указав свой аккаунт в betanet.

### 3. Скачайте и запустите nearup

Перед тем как начать, убедитесь, что ваш сервер обладает общедоступным ip-адресом. Запустите [nearup по пошаговой инструкции](https://github.com/near/nearup) и запустите ноду с помощью образа в докере или исходников `nearcore`.

### 4. Запустите стекинг токенов из своего кошелька \(без делигирования\)

#### Запустите nearup в режиме валидатора

Когда вы запускаете `nearup` в первый раз, то потребуется ввести account id в betanet для генерации файла `validator_key` для подписи блоков. Эти ключи хранятся в`validator_key.json` и содержат информацию об аккаунте, а также пару ключе для валидации.

```text
cat ~/.near/betanet/validator_key.json
```

Если появляется следующая ошибка при обращении  к`validator_key.json,` значит ключ валидации не был создан

```text
nearkat@nearkat ~ % cat ~/.near/betanet/validator_key.json |grep "public_key"
cat: /Users/nearkat/.near/betanet/validator_key.json: No such file or directory
```

и чтобы его сгенерировать, потребуется сбросить папку  `.near`:

1. Останавливаем ноду `nearup stop`
2. Очищаем папку `~/.near` 
3. Перезапускаем ноду `nearup betanet` 

**Внимание:** при очистке папки `.near` будут также удалены ключи Следовательно, все созданные транзакции для стекинга надо будет заново подписать новым ключом.

При отсутствии файлов в папке `.near` , `nearup` заново запросит account ID в betanet. В случае успеха вы уведите примерно следующее:

```text
Pull docker image nearprotocol/nearcore:beta
Setting up network configuration.
Enter your account ID (leave empty if not going to be a validator): zavodil.betanet
Generating node key...
Node key generated
Generating validator key...
Validator key generated
Stake for user 'zavodil.' with 'ed25519:8qfcviFnPucrMvN19f1hoVKhhtntGjosRHNJzaXiRem7'
Starting NEAR client docker...
Node is running! 
To check logs call: docker logs --follow nearcore
```

По данному тексту можно удостовериться, что для юзера zavodil был автоматически сгенерированы ключи и получен публичный ключ`ed25519:8qfcviFnPucrMvN19f1hoVKhhtntGjosRHNJzaXiRem7`

**Внимание:** Запишите этот публичный ключ, он нам еще пригодится позднее.

#### Открываем стейкинг 

Возвращаемся к `near-shell` и указываем что работаем с `betanet`:

Вводим команду для создание стейкинга

```text
near stake <YOUR_ACCOUNT_ID> <VALIDATOR_KEYS_PUBLIC_KEY> <AMOUNT> --walletUrl https://wallet.betanet.nearprotocol.com --helperUrl https://helper.betanet.nearprotocol.com --nodeUrl https://rpc.betanet.nearprotocol.com
```

**Внимание:** Размер стейкинга должен быть указан в NEAR, причем на аккаунте должно содержаться достаточное количество токенов. Не кладите в стейкинг 100% вашего баланса, оставьте необходимое количество токенов для работы смартконтракта и оплаты хранения данных аккаунта \(100 $NEAR будет более чем достаточно\).

Вы можете получить следующую ошибку, если не указали что нода должна обращаться к `betanet` :

```text
nearkat@nearkat ~ % near stake zavodil.betanet ed25519:8qfcviFnPucrMvN19f1hoVKhhtntGjosRHNJzaXiRem7 100
Using options: {
  networkId: 'default',
  nodeUrl: 'https://rpc.nearprotocol.com',
  contractName: undefined,
  walletUrl: 'https://wallet.nearprotocol.com',
  helperUrl: 'https://helper.nearprotocol.com',
  accountId: 'zavodil.betanet',
  stakingKey: 'ed25519:8qfcviFnPucrMvN19f1hoVKhhtntGjosRHNJzaXiRem7',
  amount: '100'
}
Staking 100 (100000000000000000000000000) on zavodil with public key = ed25519:8qfcviFnPucrMvN19f1hoVKhhtntGjosRHNJzaXiRem7.
Error:  TypedError: [-32000] Server error: account nearkat does not exist while viewing
    at JsonRpcProvider.sendJsonRpc (/usr/local/lib/node_modules/near-shell/node_modules/near-api-js/lib/providers/json-rpc-provider.js:113:27)
    at processTicksAndRejections (internal/process/task_queues.js:97:5)
    at async JsonRpcProvider.query (/usr/local/lib/node_modules/near-shell/node_modules/near-api-js/lib/providers/json-rpc-provider.js:60:24)
    at async Account.fetchState (/usr/local/lib/node_modules/near-shell/node_modules/near-api-js/lib/account.js:45:23)
    at async Account.state (/usr/local/lib/node_modules/near-shell/node_modules/near-api-js/lib/account.js:52:9)
    at async Near.account (/usr/local/lib/node_modules/near-shell/node_modules/near-api-js/lib/near.js:40:9)
    at async exports.stake (/usr/local/lib/node_modules/near-shell/index.js:179:21)
    at async Object.handler (/usr/local/lib/node_modules/near-shell/utils/exit-on-error.js:4:9) {
  type: 'UntypedError'
}
```

Чтобы исправить ошибку, запустите команду `export NODE_ENV=betanet` и повторите транзакцию для создания стейкинга.

### 5. Получите токены для стейкинга через смарт-контракт делегирования \(в разработке\)

Пользователи смогут делегировать свои токены на ноды валидаторов как только соответствующий смарт-контракт будет опубликован.

## Проверка работоспособности

Существует 5 разных способов верифицировать, что транзакция для открытия стейкинга попала в блокчейн:

1. Корректный ответ на транзакцию в `near-shell`
2. Новое событие в блок-эксплорере `betanet`
3. Правильное отображение баланса аккаунта, полученного командой `near state` 
4. Изменения в логах `nearup` отображаемое заглавной буквой "V"
5. Запрос JSON RPC, отправленный на endpoint `validator` 
6. Проверка транзакции. Вы получили транзакцию после открытия стейкинга которая содержит строку `status: { SuccessValue: '' }` и выглядит примерно так:

```text
nearkat@nearkat ~ $ near stake nearkat ed25519:BE8hs6LuFbG5j1C2tLXKUT2NqRLbCxwBCKXqte9qZ1HB 70000
Using options: {
  networkId: 'betanet',
  nodeUrl: 'https://rpc.betanet.nearprotocol.com',
  contractName: undefined,
  walletUrl: 'https://wallet.betanet.nearprotocol.com',
  helperUrl: 'https://helper.betanet.nearprotocol.com',
  accountId: 'nearkat',
  stakingKey: 'ed25519:BE8hs6LuFbG5j1C2tLXKUT2NqRLbCxwBCKXqte9qZ1HB',
  amount: '70000'
}
Staking 70000 (70000000000000000000000000000) on nearkat with public key = ed25519:BE8hs6LuFbG5j1C2tLXKUT2NqRLbCxwBCKXqte9qZ1HB.
{
  status: { SuccessValue: '' },
  transaction: {
    signer_id: 'nearkat',
    public_key: 'ed25519:8GQ3X1fuKdprwwkHUxi4bXj2ux9Bdm6gMJdgFdWk6hGc',
    nonce: 7,
    receiver_id: 'nearkat',
    actions: [
      {
        Stake: {
          stake: '70000000000000000000000000000',
          public_key: 'ed25519:BE8hs6LuFbG5j1C2tLXKUT2NqRLbCxwBCKXqte9qZ1HB'
        }
      }
    ],
    signature: 'ed25519:4ryh1uxbPVsoFuqAsyTowupGfpwz3XuaEnYw6fmQ1Q3nrLXgUL362RGZwFo2wKWJaSmJirqDnMtpiSzrH4DCHBQ2',
    hash: 'FTTzoTpGVjXN8sSTKrRTt9RBnVZs7XsKnYXc8nF8mjAu'
  },
  transaction_outcome: {
    proof: [
      {
        hash: '5eCuCp2a5yHjF1BuPrbpccW4VKvs9m3S6UNnkU3LUpMS',
        direction: 'Right'
      }
    ],
    block_hash: 'CLMZnLxzi5CzKepMRTqPk5wdMwMhwHu42YnYSfQoJDKK',
    id: 'FTTzoTpGVjXN8sSTKrRTt9RBnVZs7XsKnYXc8nF8mjAu',
    outcome: {
      logs: [],
      receipt_ids: [ 'Atpwa7tbbBizZ8bVJusMZ7dvUTCECksXxKxT69BxnMdm' ],
      gas_burnt: 924119500000,
      status: {
        SuccessReceiptId: 'Atpwa7tbbBizZ8bVJusMZ7dvUTCECksXxKxT69BxnMdm'
      }
    }
  },
  receipts_outcome: [
    {
      proof: [
        {
          hash: '8fNXhpjrs4iyPPbzTYUr1V4zM73LdeohocGMCTxJUBB6',
          direction: 'Left'
        }
      ],
      block_hash: 'CLMZnLxzi5CzKepMRTqPk5wdMwMhwHu42YnYSfQoJDKK',
      id: 'Atpwa7tbbBizZ8bVJusMZ7dvUTCECksXxKxT69BxnMdm',
      outcome: {
        logs: [],
        receipt_ids: [],
        gas_burnt: 924119500000,
        status: { SuccessValue: '' }
      }
    }
  ]
}
```

1. Зайдите в [betanet explorer](https://explorer.betanet.nearprotocol.com/) и проверьте, что транзакция завершена успешна:
2. Запустите`near state --walletUrl https://wallet.betanet.nearprotocol.com --helperUrl https://helper.betanet.nearprotocol.com --nodeUrl https://rpc.betanet.nearprotocol.com` и убедитесь, что на аккаунте есть необходимое количество токенов в состоянии locked:

```text
nearkat@nearkat ~ $ near state zavodil.betanet
Using options: {
  networkId: 'betanet',
  nodeUrl: 'https://rpc.betanet.nearprotocol.com',
  contractName: undefined,
  walletUrl: 'https://wallet.betanet.nearprotocol.com',
  helperUrl: 'https://helper.betanet.nearprotocol.com',
  accountId: 'zavodil.betanet'
}
Account zavodil.betanet
{
  amount: '58957995048254107744134739414',
  locked: '100000000000000000000000000',
  code_hash: '11111111111111111111111111111111',
  storage_usage: 510,
  storage_paid_at: 0,
  block_height: 2556149,
  block_hash: '3J8VBP5Yrooz3zoFhNHy6G15arkyuzg4QkYdtBsc3fUj',
  formattedAmount: '58,957.995048254107744134739414'
}

```

Здесь можно увидеть количество токенов в стейке в строке `locked:` . Значение представлено в yoctoNEAR \(10^-24 NEAR\).

1. Через 2 эпохи \(24 часа\), вы увидите, что ваша нода присутствует в логах со статусом `V/n` , которая означает что это валидатор:

```text
Apr 14 21:44:57.128  INFO stats: # 2556590 XzssUzbfUmtAduVBjob2uGWK6hU8JP8jk4zwm11wRTN V/6 16/15/40 peers ⬇ 65.3kiB/s ⬆ 71.0kiB/s 1.30 bps 0 gas/s CPU: 8%, Mem: 1.2 GiB    
Apr 14 21:45:07.130  INFO stats: # 2556603 6dpBFao9M1MWRZZe79ohn3z7oAZYzW5yFMCkyu89piQ8 V/6 16/15/40 peers ⬇ 64.2kiB/s ⬆ 71.0kiB/s 1.30 bps 0 gas/s CPU: 8%, Mem: 1.2 GiB    
Apr 14 21:45:17.132  INFO stats: # 2556615 BJHBXF9mPqGeLjnbiPR8Y28NjZppZDjRTyiDFLNVbF2G V/6 16/15/40 peers ⬇ 61.9kiB/s ⬆ 72.5kiB/s 1.20 bps 0 gas/s CPU: 7%, Mem: 1.2 GiB    
```

Статус`V/6` rсразу после хэша блока говорит о том, что вы являетесь одним из 6 валидаторов. Если статус `-/6` , это значит что либо ваша нода не является валидатором, либо обладает недостаточным количеством токенов в стейкинге. Иногда можно увидеть такой статус как`F/10`: что означает, что размер вашего стейкинг был слишком маленьким и в данный момент вы являетесь рыбаком \(fisherman\) и не получаете награду.

1. Отправьте запрос в блоек-эксплорер Betanet через JSON RPC, чтобы получить набор заявок:

```text
curl -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' https://rpc.betanet.nearprotocol.com -v
```

В ответ вы получите JSON, содержащий следующие параметры:

* current\_validators \(текущие валидаторы\)
* next\_validators \(следующие валидаторы\)
* current\_fishermen \(текущие рыбаки\)
* next\_fishermen \(будущие рыбаки\)
* **current\_proposals \(текущие заявки\)** **&lt;&lt;  ваша транзакция должна быть тут**
* prev\_epoch\_kickout \(исключены за прошлую эпохи\)

Каждая запись в объекте `current_validators` содержит ожидаемый аптайм:

```text
"current_validators": [
            {
                "account_id": "zavodil.betanet",
                "public_key": "ed25519:BE8hs6LuFbG5j1C2tLXKUT2NqRLbCxwBCKXqte9qZ1HB",
                "is_slashed": false,
                "stake": "75932253155495715897593582482",
                "shards": [
                    0
                ],
                "num_produced_blocks": 106,
                "num_expected_blocks": 106
            }
    ]
```

Убедитесь, что  `num_produced_blocks` равно значению `num_expected_blocks`, иначе появляется риск, что ваша нода будет исключена \(подробнее  в статье "Обеспечение позиции валидатора" раздела [validator economics docs](https://docs.nearprotocol.com/docs/validator/economics)\).

## Остановка ноды

Сначала, требуется освободить средства, установив размер своего стейкинга как 0:

```text
near stake <YOUR_ACCOUNT_ID> <VALIDATOR_KEYS_PUBLIC_KEY> 0 --walletUrl https://wallet.betanet.nearprotocol.com --helperUrl https://helper.betanet.nearprotocol.com --nodeUrl https://rpc.betanet.nearprotocol.com
```

Далее можно подождать 2 эпохи \(6 часов\) и выключить свою ноду. Если отключить валидатора без этого ожидания, это повлечет негативное воздействие на быстродействие всей сети и вызовет недовольство других пользователей.

Чтобы остановить ноды, достаточно выполнить команду:

```text
nearup stop
```

{% page-ref page="stakewars.md" %}

