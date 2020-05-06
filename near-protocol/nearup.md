---
description: 'Инструкция для нод в devnet, betanet и testnet'
---

# Запуск ноды Nearup в Near Protocol

{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардингом на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

{% hint style="info" %}
Запуск ноды Nearup - часть программы [StakeWars](https://nodes.cryptasutra.com/near-protocol/stakewars).
{% endhint %}

### Подготовка

Перед началом работы, убедитесь, что на вашем сервере установлено следующее программное обеспечение:

* Python 3
* git 
* cURL 

В Ubuntu их можно разом установить следующей командой:

```text
sudo apt install python3 git curl
```

### Установка ноды Nearup

```text
curl --proto '=https' --tlsv1.2 -sSfL https://up.near.dev | python3
```

Nearup автоматически добавляет себя в переменную окружения PATH, которая будет переменена после перезапуска терминала либо выполнения команды `source ~/.profile`. При каждом запуске nearup автоматически обновляется до новейшей версии.

### Запуск

{% hint style="info" %}
Внимание! После запуска nearup попросит Account ID, это описано в [инструкции по стейкингу](https://nodes.cryptasutra.com/near-protocol/staking).
{% endhint %}

#### Использование контейнера в Docker \(рекомендуется для ускорения процесса первого запуска\)

> Внимание, тут потребуется установка [Docker](https://docs.docker.com/get-docker/). В Ubuntu его можно установить следующей командой:
>
> ```text
> sudo apt install docker.io
> ```
>
> Также убедитесь, что ваш юзер добавлен в группу `docker`:
>
> ```text
> sudo usermod -aG docker your-user
> ```
>
> Не забудьте заменить `your-user` на имя вашего юзера в системе, его всегда можно узнать, запустив команду `whoami`.

Когда Docker установлен, просто запустите команду:

```text
nearup betanet
```

Возможны разные варианты запуска: `betanet` - это еженедельно обновляемый релиз;`devnet` - ежесуточный релиз, а `testnet` - более стабильный релиз.

Для просмотра логов, запустите команду

```text
docker logs --follow nearcore
```

#### Использование официальных двоичных файлов \(рекомендуется для запуска на серверах\)

Сейчас эти двоичные файлы доступны для запуска в Linux и Mac OS X.

```text
nearup betanet --nodocker
```

Замените `betanet` на `devnet` или `tеstnet`, если хотите подключиться к другой сети.

#### Использование исходников для компиляции собственных двоичных файлов \(рекомендуется для валидаторов и разработчиков\)

Склонируйте и скомпилируйте [nearcore](https://github.com/nearprotocol/nearcore) командой`make release` или`make debug` .

```text
nearup betanet --nodocker --binary-path path/to/nearcore/target/{debug, release}
```

`betanet` также можно заменить на `devnet` или `testnet` .

### Создание локальной сети

Склонируйте и скомпилируйте [nearcore](https://github.com/nearprotocol/nearcore) командой`make release` или`make debug` .

```text
nearup localnet --binary-path path/to/nearcore/target/{debug, release}
```

По умолчанию это породит 4 ноды в 1 шарде. Номера RPC портов каждой ноды будут последовательно расти, начиная с 3030. Доступ к статусу каждой ноды возможен при обращении к [http://localhost:3030/status](http://localhost:3030/status)

### Остановка ноды или всех нод в локальной сети

```text
nearup stop
```

### Дополнительные опции

```text
nearup betanet --help
```

## Изменение кодовой базы

Чтобы запустить локально измененный код nearup, потребуется:

1. Сделать fork/clone [репозитория](https://github.com/near/nearup) и checkout вашего бранча
2. Изменить код
3. Запустить`python3 main.py ...`. Если вы меняется основной бранч \(branch\), это будет идентично запуску `nearup ...`

{% hint style="info" %}
После запуска ноды nearup, там можно создать [стейкинг](https://nodes.cryptasutra.com/near-protocol/staking).
{% endhint %}

{% hint style="info" %}
Инструкция для установки в среде MacOs [доступна на английском языке](https://github.com/near/nearup#run-nearup-on-macos).
{% endhint %}

{% page-ref page="nearup.md" %}



