# Переход ноды NEAR из Testnet в Mainnet

### Подготовка к переезду

Если вы являетесь валидатором testnet, то снимите свой стейк и дождитесь исключения из числа валидаторов

```text
near call my_validator unstake '{"amount": "100000000000000000000000000"}' --accountId user1
```

Остановите ноду

```text
nearup stop
```

## Подготовка ноды в Mainnet

### 1. Создайте майннет-кошелек

### 2. Разверните контракт стейкинг-пула

Откройте [near-cli](https://github.com/near/near-cli), с помощью команды `near call`:

```text
near call poolv1.near create_staking_pool '{"staking_pool_id":"", "owner_id":"", "stake_public_key":"", "reward_fee_fraction": {"numerator": , "denominator": }}' --account_id  --amount 30 --gas 300000000000000
```

Здесь бы запускаем метод `staking_pool_factory` из [NEAR Core Contracts](https://github.com/near/core-contracts), передавая следующие параметры:

* `poolv1.near` название фабрики для контрактов
* `POOL_ID` имя вашего контракта валидатора \(1 слово, без указания названия фабрики\)
* `OWNER_ID` кошелек, который контролирует пул
*  `VALIDATOR_KEY` - это значение переменной public key в файле`~/.near/validator_key.json` с данного сервера. Файл не появится, пока нода не будет запущена, можно использовать старые файлы из testnet или betanet.
*  `{"numerator": <X>, "denominator": <Y>}` устанавливают комиссию ноды. Чтобы задать 10% сборов, установите x=10 и y=100.
* `--amount 30` сразу отправьте 30 $NEAR, чтобы обслуживать хранение контракта
* `--gas 300000000000000` выделите газ на операцию \(не обязательно\)

{% hint style="info" %}
Если вы хотите, чтобы ваш контракт назывался `buildlinks.poolv1.near`, то укажите POOL\_ID как `buildlink`
{% endhint %}

### 3. Скомпилируйте и запустите ноду NEAR MainNet 

Уточните официальную версию [nearcore](https://github.com/nearprotocol/nearcore/releases/tag/1.11.1) для ноды майннета. Это не обязательно должна быть самая свежая из версий. Например: `1.11.1` .

Склонируйте код из github

```text
 git clone --branch 1.11.1 https://github.com/nearprotocol/nearcore.git
 cd nearprotocol
```

Скомпилируйте бинарные файлы с параметром **`--release` :**

```text
cargo build -p neard --release
target/release/neard init --chain-id="mainnet"
```

Откройте файл `~/.near/config.json` и замените его на содержимое [этого файла](https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/mainnet/config.json). \(уточните более актуальный конфиг майннет ноды в случае необходимости\).

Убедитесь, что существует файл  `~/.near/validator_key.json` и public key там равен `VALIDATOR_KEY ,` что указан в контракте, а имя соответствует полному имени аккаунта, например  `buildlinks.poolv1.near` .

Запустите ноду командой

```text
target/release/neard run
```

{% hint style="info" %}
Вам не обязательно использовать папку ~/.near/ для хранения файлов, можно выбрать любую другую папку, указав её в параметре home, например, `target/release/neard --home=/root/.near-mainnet run`
{% endhint %}

{% hint style="info" %}
Синхронизация ноды - долгий процесс, не надо его прерывать если растёт количество обработанных блоков. Также контролировать свой прогресс можно на странице [https://explorer.near.org/nodes/online-nodes](https://explorer.near.org/nodes/online-nodes) в разделе Online Nodes.
{% endhint %}

Когда нода сихнронизирована, отправьте токены в делегацию на данный контракт.

