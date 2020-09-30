# Делегирование токенов в NEAR Mainnet

{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардингом на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

Вы можете отправить токены в делегирование валидаторам Mainnet, чтобы таким образом увеличить безопасность сети NEAR и взамен зарабатывать вознаграждение от инфляции сети, пропорционально своему стейку. Обратите внимание, что при этом валидатор не получает контроль над вашими токенами, а вы можете в любой момент прервать делегирование. В стейкинг можно отправить даже заблокированные \(locked\) токены.

{% hint style="warning" %}
В отличие от многих других блокчейнов, стейкинг в NEAR не является частью протокола, а совершается на уровне смарт-контактов. Это означает, что разные валидаторы могут предоставлять разные условия для своих делегаторов.
{% endhint %}

Прежде чем начать делегирование, уточните, являются ли ваши токены незаблокироваными \(покупка на CoinList в опции 1\) или заблокироваными \(покупка на CoinList в опциях 2 и 3, гранты\) и выберите стейкинг-пул.

### Выбор стейкинг-пула

Список валидаторов, принимающих токены в делегировании можно посмотреть следующими способами:

* Команда /stakingpools в телеграм-боте [@nearup\_bot](%20https://t.me/nearup_bot). Вывод текущих стейкинг-пулов с указанием стейка и комиссии пула в порядке увеличения комиссии / уменьшения стейка.
* Веб-интерфейс [https://staking.dokia.cloud/staking/near/validators](https://staking.dokia.cloud/staking/near/validators) \(упорядочены по алфавиту\)

Вы вольны выбрать любой стейкинг-пул, поддерживая децентрализацию и выбирая подходящую комиссию пула. Чем меньше комиссия пула, тем больше ваш доход.

{% hint style="info" %}
Имена стейкинг-пулов в майннет сейчас заканчиваются на `poolv1.near`
{% endhint %}

Название пула автора данной статьи, ведущего канала [Zavodil](http://www.youtube.com/c/Zavodil%20), создателя чата [@near\_protocol](https://t.me/near_protocol), бота [@nearup\_bot](%20https://t.me/nearup_bot) и победителя хакатона NEAR на gitcoin: `zavodil.poolv1.near`. Там установлена комиссия пула в 1%. Буду рад всем делегаторам!

### Делегирование незаблокированных токенов через CLI

Ваш потребуется установка [NearUp](https://nodes.cryptasutra.com/near-protocol/ustarevshee/nearup) и запуск методов в [контрактах стейкинг-пула](https://nodes.cryptasutra.com/near-protocol/ustarevshee/staking-pool-contact).

1. Убедитесь, что вы подключены к mainnet `export NODE_ENV=mainnet`
2. Выполните login \([подробнее на русском языке](https://nodes.cryptasutra.com/near-protocol/ustarevshee/staking#otpravka-tranzakcii-na-sozdanie-steikinga)\).
3. Отправьте токены в делегирование \(оставив на аккаунте хотя бы 10 NEAR\)

```text
near call <<staking_pool>> deposit_and_stake '{"amount": "<<amount>>"}' --accountId <<account_id>> --gas 200000000000000
```

Где &lt;&lt;staking\_pool&gt;&gt; - имя стейкинг-пула, &lt;&lt;amount&gt;&gt; - размер стейка для делегирования в yoctoNear, а &lt;&lt;account\_id&gt;&gt; - ваш аккаунт, которым вы выполнили near login. Имена надо вводить без спец символов \(`@, <>` не требуются\). Значение в yoctoNear можно получить командой /convert в телеграм-боте[ ](%20https://t.me/nearup_bot)[@nearup\_bot](%20https://t.me/nearup_bot), для примера `1 NEAR = 1000000000000000000000000 yoctoNear`.

### Делегирование заблокированных токенов через CLI

Ваш аккаунт в этом случае получит доступ на lockup контрактом, где хранятся заблокированные токены. Для открытия стейкинга надо узнать имя lockup-контракта, эта информация официально рассылается по емейл, её также можно получить командой /getLockupAccount в телеграм-боте [@nearup\_bot](%20https://t.me/nearup_bot). Имя lockup-контракта выглядит примерно так: `9a827619b2bd37193af1255448659c5f575bc0c4.lockup.near`

1. Убедитесь, что вы подключены к mainnet `export NODE_ENV=mainnet`
2. Выполните login с аккаунта, который управляет lockup-аккаунтом \([подробнее на русском языке](https://nodes.cryptasutra.com/near-protocol/ustarevshee/staking#otpravka-tranzakcii-na-sozdanie-steikinga)\).
3. Выберите стейкинг-пул

   ```text
   near call <<lockup_contract>> select_staking_pool '{"staking_pool_account_id": "<<staking_pool>>"}' --accountId <<account_id>> 
   ```

4. Отправьте токены в делегирование \(оставив на аккаунте хотя бы 10 NEAR\)

   ```text
   near call <<lockup_contract>> deposit_and_stake '{"amount": "<<amount>>"}' --accountId  <<account_id>> --gas 200000000000000
   ```

Где &lt;&lt;lockup\_contract&gt;&gt; - имя вашего lockup-контракта, &lt;&lt;staking\_pool&gt;&gt; - имя стейкинг-пула, &lt;&lt;amount&gt;&gt; - размер стейка для делегирования в yoctoNear, а &lt;&lt;account\_id&gt;&gt; - ваш аккаунт, которым вы выполнили near login. Имена надо вводить без спец символов \(`@, <>` не требуются\). Значение в yoctoNear можно получить командой /convert в телеграм-боте[ ](%20https://t.me/nearup_bot)[@nearup\_bot](%20https://t.me/nearup_bot), для примера `1 NEAR = 1000000000000000000000000 yoctoNear`.

### Снятие токенов с делегирования через CLI

Чтобы снять делегирования, сначала потребуется запустить `unstake`:

```text
near call <<staking_pool>> unstake_all '{}' --accountId <<account_id>> gas=125000000000000
```

А потом, через 3 эпохи, запустить `withdraw`:

```text
near call <<staking_pool>> withdraw_all_from_staking_pool --accountId <<account_id>> --gas=175000000000000
```

{% hint style="warning" %}
Внимание, для заблокированных lockup-контрактов, вместо `staking_pool` укажите `lockup_contract`.
{% endhint %}

### Создание делегации через официальный веб-кошелек

В данный момент недоступно, следите за обновлениями в группе [https://t.me/near\_protocol](https://t.me/near_protocol).

### Создание делегирования через сторонний веб-сайт:

Только для разблокированных токенов: [https://staking.dokia.cloud/staking/near/validators](https://staking.dokia.cloud/staking/near/validators)

