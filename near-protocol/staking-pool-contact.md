---
description: Включаем делегирование в сети NEAR
---

# Cмарт-контракт стейкинг-пула NEAR

{% hint style="info" %}
[NEAR](https://near.org/) - публичный блокчейн с шардингом на консенсусе Proof of Stake, созданный сильной командой, которая уже создавала единственные в своем роде шардированные базы данных. В NEAR делают акцент на удобстве для разработчиков. Платформа эффективна для решения спектра задач: от безопасного управления активами и удостоверения личности до выполнения высокомасштабируемых децентрализованных приложений. NEAR привносит мощь Open Web в блокчейн.

Вступайте в нашу группу в телеграме [https://t.me/near\_protocol](https://t.me/near_protocol) для получения ответов на вопросы.
{% endhint %}

Контракт реализует стандарт [https://github.com/nearprotocol/NEPs/pull/27](https://github.com/nearprotocol/NEPs/pull/27) и предоставляет возможность другим пользователям делегировать средства конкретной валидирующей ноде.

В контракте фигурируют три разные роли:

* Аккаунт с контрактом для стейкинг-пула  `my_validator`. Аккаунт без ключа, который объединяет средства делегаторов в пуле.
* Владелец стейкинг-контракта `owner`. Владелец запускает ноду-валидатора от имени аккаунта с контрактом стейкинг-пула.
* Аккаунты делегаторов `user1`, `user2`, и т.д., которые хотят делегировать свои средства в пул  владельца смарт-контракта.

Владелец может настроить такой контракт и использовать его на своей ноде для валидации сети. Любой другой пользователь может отправить свои токены на этот контракт, которые будут объединены в общем стейке. Эти пользователи получают вознаграждение \(за вычетом платы, установленной владельцем\). Затем они могут разблокировать свой стейкинг и вывести личный баланс после истечения некоторого периода разблокировки. 

### Особенности реализации стейкинг-пула

Для безопасной работы стейкинг-пула в контракте не должно быть ключей доступа. В противном случае с аккаунта контракта можно создать транзакцию, нарушающую гарантии контракта. 

После того, как пользователи внесут токены в контракт, они могут открыть стейкинг на весь объем или его часть, чтобы получать долю стейка.  Награда доли в стейкинге может быть определена как общее количество токенов в стейкинге поделенное на общее количество долей стейка. Количество долей стейка всегда меньше чем число застейканных токенов, то есть цена отдельного стейка будет всегда менее  `1`.

> В соответствии с контрактом, если три пользователя делегируют 10N, 20N, 70N, то валидатор сможет сделать стейк от своего аккаунта на 100N, за которые ему распределятся места валидатора \(validator seats\). За производство блоков он будет получать награду и вот награда будет распределена между делегаторами в соответствии с их долями вклада: 10/100 награды засчитается первому, 20/100 — второму, 70/100 — третьему.

### Инициализация

Контракт должен быть инициализирован со следующими параметрами::

* `owner_id` - `string` account ID владельца контракта. Этот аккаунт сможет вызывать методы, доступные только владельцу, например, `owner`
* `stake_public_key` - `string` первичный публичный ключ, который будет использоваться для стейкинга от имени учетной записи контракта в кривой base58 ED25519. Например,`KuTCtARNzxZQ3YvXDeLjx83FDqxv2SdQTSbiq876zR7`
* `reward_fee_fraction` - `json serialized object` начальное значение доли вознаграждения, которую владелец взимает с делегатора за обслуживание ноды. Коэффициент определяется числителем и знаменателем типа `u32`. Например, `{numerator: 10, denominator: 100}` означает`10%` комиссию награды. Коэффициент не может быть больше `1`. Знаменатель не может быть `0`.

Во время инициализации контракт проверяется правильность ввода, за затем контракт инициализируется. Контракт не должен иметь заблокированный баланс во время инициализации. 

При инициализации контракт выделяет один триллион yocto NEAR на гарантии доли стейка. Этот фонд впоследствии используется для корректировки количества застейканных и снятых со стейкинга токенов, которые потребуются из-за ошибок округления. За каждое действия открытия и закрытия стейкинга контракт может неявно потратить 1 yocto NEAR из этого фонда.

Текущее общее сальдо \(за исключением суммы гарантии доли стейка\) конвертируется в доли и может быть застейкано \(после следующего действия\). Этот баланс никогда не может быть снят со стейкинга или выведен с контракта. Он используется для поддержания минимального количества долей, а также помогает оплачивать стоимость хранения контракта, которая может со временем увеличиваться

### Аккаунты делегаторов

Контракт содержит информацию об учетной записи для каждого делегатора, ассоциированной с хешем account ID делегатора.

Информация содержит:

* Незастейканный баланс аккаунта
* Количество долей стейка
* Минимальный номер эпохи, когда баланс может быть снят. Изначально ноль. 

Делегатор может выполнять следующие действия:

#### Депозит

Когда учетная запись делегатора впервые вносит средства на контракт, создается внутренний счет, на который зачисляются все незастейканные токены.

#### Открытие стейкинга

Когда учетная запись хочет открыть стейкинг на определенную сумму, в контракте рассчитывается количество долей его стейка  \(`num_shares`\) и фактический округленный размер стейка \(`amount`\). Незастейканный баланс аккаунта уменьшается на `amount`, число долей стейка аккаунта увеличивается на `num_shares`. Контракт увеличивает общее количество застейканных токенов и общее количество долей стейка. Затем контракт обновляет стейкинг.

#### Закрытие стейкинга

Когда учетная запись хочет снять со стейкинга определенную сумму, в контракте рассчитывается количество необходимых долей стейка \(`num_shares`\) и фактически требуемая округленная сумма для снятия стейка \(`amount`\). Это рассчитывается исходя из текущей общей цены долей стейка. Незастейканный баланс аккаунта увеличивается на остаток на `amount`, количество долей стейка на аккаунте уменьшается на `num_shares`. Минимальная высота эпохи, когда аккаунт может быть вывести средства устанавливается на текущую высоту эпохи, увеличенную на `4`. Контракт уменьшает общее количество застейканных токенов и общее количество долей стейка. Затем контракт обновляет стейкинг.

#### Вывод средств

Когда учетная запись хочет оформить вывод, контракт проверяет минимальную высоту эпохи этой учетной записи и проверяет сумму. Затем отправляется перевод и уменьшается незастейканный остаток на счете. 

#### Ping

Вызывает внутреннюю функцию для распределения наград, когда меняется эпоха блокчейна. В этом случае контракт также обновляет стейкинг.

### Распределение вознаграждения

Перед каждым действием контракт вызывает метод `internal_ping`. Этот метод распределяет награды активным делегаторам, когда меняется эпоха блокчейна. Награды могут быть предоставлены вследствие стейкинга, а также за счет того, что контракт зарабатывает комиссию за газ, потраченный на каждый вызов функции.

Сначала метод проверяет, отличается ли текущая эпоха от последней, и, если она не изменена, останавливает работу метода.

Награды рассчитываются следующим образом. Контракт отслеживает последний известный общий остаток на счете. Этот баланс состоит из начального баланса контракта, а также всех остатков на счетах делегаторов \(включая владельца\) и всех накопленных вознаграждений. \(Награды за валидацию добавляются автоматически в начале эпохи, а начисление комиссии за исполнение контракта совершается после каждой транзакции\)

Когда вызывается метод, контракт использует текущий общий остаток на счете \(без приложенного депозита\) и вычитает последний общий остаток на счете. Разница - это общее вознаграждение, которое должно быть распределено.

Коэффициент вознаграждения передается владельцу контракта. Он настраивается владельцем контракта и не может превышать 1.

Оставшаяся часть вознаграждения добавляется к общему застейканному балансу. Это действие увеличивает цену каждой доли стейка, не меняя количество долей стейка, принадлежащих различным аккаунтам. На эти аккаунты распределяются вознаграждения в зависимости от долей стейка.

Вознаграждение владельца конвертируется в доли стейка по новой цене и добавляется на счет владельца. Это делается аналогично методу `stake`, но без списания непогашенного остатка на счете владельца.

Как только награды распределены, контракт запоминает новый общий баланс.

### Методы, доступные только для владельца

Владелец контракта может сделать следующее:

* Изменить публичный ключ стейкинга. Это действие обновляется стейкинг с новым ключом.
* Изменить долю вознаграждения
* Голосовать от имени пула. Это необходимо для управления блокчейном NEAR и обсуждается в следующем NEP: [https://github.com/nearprotocol/NEPs/pull/62](https://github.com/nearprotocol/NEPs/pull/62)

### Гарантии контракт стейкинг-пула

Текущая реализация стейкинг-пула гарантирует следующие требуемые свойства :

* Контракт не может потерять или заблокировать токены пользователей. 
* Если пользователь внес депозит X, пользователь должен иметь возможность снять хотя бы X. 
* Если пользователь успешно застейкал X, он может снять ставку как минимум X.
* Контракт не должен блокировать выведенные из стейкинга средства более чем на `4` эпохи после отмены стейкинга.

Другие требования:

* Контракт стейкинг-пула является безопасным, если у него нет ключей доступа.
* Цена доли стейка всегда не менее `1`. 
* Цена доли стейка никогда не уменьшается. 
* Коэффициент вознаграждения составляет от `0` до `1` включительно. 
* Владелец не может снимать средства других делегаторов. 
* Владелец не может удалить аккаунт стейкинг-пула

### Подготовка к работе

Мы будет делать контракт на языке Rust, поэтому нам потребуется

* Установить [Rustup](https://rustup.rs/):

```text
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

* Добавить wasm target:

```text
rustup target add wasm32-unknown-unknown
```

### Запускаем создание контракта

```text
./build.sh
```

### Использование

Команды для развертывания и инициализации стейкинг-контракта:

```text
near create_account my_validator --masterAccount=owner
near deploy --accountId=my_validator --wasmFile=res/staking_pool.wasm
# Initialize staking pool at account `my_validator` for the owner account ID `owner`, given staking pool and 10% reward fee.
near call my_validator new '{"owner_id": "owner", "stake_public_key": "CE3QAXyVLeScmY9YeEyR3Tw9yXfjBPzFLzroTranYtVb", "reward_fee_fraction": {"numerator": 10, "denominator": 100}}' --account_id owner
# TODO: Delete all access keys from the `my_validator` account
```

Со стороны пользователя, делегирующего средства:

```text
near call my_validator deposit '{}' --accountId user1 --amount 100
near call my_validator stake '{"amount": "100000000000000000000000000"}' --accountId user1
```

Обновление текущей награды:

```text
near call my_validator ping '{}' --accountId user1
```

Просмотр методов:

```text
# User1 total balance
near view my_validator get_account_total_balance '{"account_id": "user1"}'

# User1 staked balance
near view my_validator get_account_staked_balance '{"account_id": "user1"}'

# User1 unstaked balance
near view my_validator get_account_unstaked_balance '{"account_id": "user1"}'

# Whether user1 can withdraw now
near view my_validator is_account_unstaked_balance_available '{"account_id": "user1"}'

# Total staked balance of the entire pool
near view my_validator get_total_staked_balance '{}'

# Owner of the staking pool
near view my_validator get_owner_id '{}'

# Current reward fee
near view my_validator get_reward_fee_fraction '{}'

# Owners balance
near view my_validator get_account_total_balance '{"account_id": "owner"}'
```

Чтобы снять делегирования, сначала потребуется запустить `unstake`:

```text
near call my_validator unstake '{"amount": "100000000000000000000000000"}' --accountId user1
```

А потом, через 3 эпохи, запустить `withdraw`:

```text
near call my_validator withdraw '{"amount": "100000000000000000000000000"}' --accountId user1
```

### Интерфейс

```text
pub struct RewardFeeFraction {
    pub numerator: u32,
    pub denominator: u32,
}

/// Initializes the contract with the given owner_id, initial staking public key (with ED25519
/// curve) and initial reward fee fraction that owner charges for the validation work.
#[init]
pub fn new(
    owner_id: AccountId,
    stake_public_key: Base58PublicKey,
    reward_fee_fraction: RewardFeeFraction,
);

/// Distributes rewards and restakes if needed.
pub fn ping(&mut self);

/// Deposits the attached amount into the inner account of the predecessor.
#[payable]
pub fn deposit(&mut self);

/// Withdraws the non staked balance for given account.
/// It's only allowed if the `unstake` action was not performed in the recent 3 epochs.
pub fn withdraw(&mut self, amount: U128);

/// Stakes the given amount from the inner account of the predecessor.
/// The inner account should have enough unstaked balance.
pub fn stake(&mut self, amount: U128);

/// Unstakes the given amount from the inner account of the predecessor.
/// The inner account should have enough staked balance.
/// The new total unstaked balance will be available for withdrawal in 3 epochs.
pub fn unstake(&mut self, amount: U128);

/****************/
/* View methods */
/****************/

/// Returns the unstaked balance of the given account.
pub fn get_account_unstaked_balance(&self, account_id: AccountId) -> U128;

/// Returns the staked balance of the given account.
/// NOTE: This is computed from the amount of "stake" shares the given account has and the
/// current amount of total staked balance and total stake shares on the account.
pub fn get_account_staked_balance(&self, account_id: AccountId) -> U128;

/// Returns the total balance of the given account (including staked and unstaked balances).
pub fn get_account_total_balance(&self, account_id: AccountId) -> U128;

/// Returns `true` if the given account can withdraw tokens in the current epoch.
pub fn is_account_unstaked_balance_available(&self, account_id: AccountId) -> bool;

/// Returns the total staking balance.
pub fn get_total_staked_balance(&self) -> U128;

/// Returns account ID of the staking pool owner.
pub fn get_owner_id(&self) -> AccountId;

/// Returns the current reward fee as a fraction.
pub fn get_reward_fee_fraction(&self) -> RewardFeeFraction;

/*******************/
/* Owner's methods */
/*******************/

/// Owner's method.
/// Updates current public key to the new given public key.
pub fn update_staking_key(&mut self, stake_public_key: Base58PublicKey);

/// Owner's method.
/// Updates current reward fee fraction to the new given fraction.
pub fn update_reward_fee_fraction(&mut self, reward_fee_fraction: RewardFeeFraction);

/// Owner's method.
/// Vote on a given proposal on a given voting contract account ID on behalf of the pool.
/// NOTE: This method allows the owner to call `vote(proposal_id: U64)` on any contract on
/// behalf of this staking pool.
pub fn vote(&mut self, voting_account_id: AccountId, proposal_id: ProposalId) -> Promise;
```

### Переход стейкинга от аккаунта на стейкинг от контракта делегации

В целом, стейкинг от своего аккаунта или через делегацию это параллельные вещи, если в распоряжении имеется много токенов и есть желение иметь два запущенных узла, то можно продолжать держать узел с обычным stake и добавить узел с staking pool. 

Если требуется на том же сервере те же токены запустить через staking pool, то нужно сделать: 

1. unstake 
2. создать аккаунт для pool \(по-хорошему, на аккаунте для staking pool не должно быть больше никаких AccessKey, это гарантирует, что никто не сможет токены делегированные на stake просто взять и вывести\)
3. загрузить туда контракт 
4. инициализировать контракт с validator\_key публичным ключом 
5. делегировать туда деньги со своего аккаунта

