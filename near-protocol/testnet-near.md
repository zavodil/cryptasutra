---
description: 'Инструкция для тех, кто перемещает свою ноду с betanet на testnet'
---

# Создание контракта стейкинг-пула в Testnet Near

### Создание кошелька

Кошелек в testnet создается по адресу [https://wallet.testnet.near.org/](https://wallet.testnet.near.org)

### Подготовка ноды

Установите [необходимое программное обеспечение](https://nodes.cryptasutra.com/near-protocol/staking#nastroika-okruzheniya). Требования к серверу:

> * Хотя бы 2 CPU 
> * Хотя бы 4GB RAM 
> * Хотя бы 100 GB свободного места на диске

Переключите окружение на testnet

```
export NODE_ENV=testnet
```

Запустите ноду и проведите авторизацию аналогично [процедуре betanet](https://nodes.cryptasutra.com/near-protocol/staking#otpravka-tranzakcii-na-sozdanie-steikinga).

```text
near login
nearup testnet
```

Создайте аккаунт для управления пулом

```text
near create_account <POOL_ID> --masterAccount=<OWNER_ID>
```

Создайте контракт стейкинг-пула

```text
near call pool.6fb1358 create_staking_pool '{"staking_pool_id":"<POOL_ID>", "owner_id":"<OWNER_ID>", "stake_public_key":"<VALIDATOR_KEY>", "reward_fee_fraction": {"numerator": <X>, "denominator": <Y>}}' --account_id <OWNER_ID> --amount 30 --gas 300000000000000
```

 `VALIDATOR_KEY` - это значение переменной public key в файле`~/.near/testnet/validator_key.json` с данного сервера. Файл не появится, пока нода не будет запущена в testnet.

 `{"numerator": <X>, "denominator": <Y>}` устанавливают комиссию ноды. Чтобы задать 10% сборов, установите x=10 и y=100.

Контракт `pool.6fb1358` - это новая версия контракта `stakingpool` \(который использовался ранее\). Новый контракт имеет механизм голосования. После этого ваш адрес будет `<POOL_ID>.pool.6fb1358`.

### Получение делегирования в стейкинг-пул

[Заполните форму](https://nearprotocol1001.typeform.com/to/x4Bval) для получения стейка в делегирование от разработчиков.

