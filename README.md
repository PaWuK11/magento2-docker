Magento 2 Docker
================
Installing Docker and Docker Compose
-----------------------------------
### Ubuntu
Встановлення Docker:
- `sudo apt-get update`
- `sudo apt-get install apt-transport-https ca-certificates curl software-properties-common`
- `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
- `sudo add-apt-repository \ "deb [arch=amd64] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) \ stable"`
- `sudo apt-get update`
- `sudo apt-get install docker-ce`

Встановлення Docker Compose:

- `sudo apt-get install python-pip`
- `sudo pip install docker-compose`

Пофіксити завісімості:

- `sudo groupadd docker`
- `sudo gpasswd -a $USER docker`
- `newgrp docker`


----
Troubleshooting
---------------

- **Xdebug не працює на MacOS** - перевірте значення змінної `xdebug.remote_connect_back` у файлі `config/php/php.ini`, воно має бути рівним `0` або `Off`

- **docker-compose не оновлюється** - при встановленні через `pip install docker-compose` встановлення відбувається в директорію `~/.local/bin/docker-compose`, тоді як правильне місце буде `/usr/local/bin/docker-compose`. 
Необхідно деінсталювати `pip uninstall docker-compose`
- **invalid port in upstream "${PHP_HOST_XDEBUG}** - оновити локальні імеджі контейнерів, виконайте команду `docker-compose pull`
- **Configuration for volume code specifies "mountpoint" driver_opt...** - оновити `docker-compose`, `sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`

Plugin Xdebug
-------------
*__Примітка__: Для виконання команд `composer` або роботи з `bin/magento` використовуйте контейнер `magento2_php`, а не `magento2_php_xdebug`, це пришвидшить роботу.*

Для **Xdebug** потрібно встановити плагін **Xdebug helper**, який допомагає швидко вмикати та вимикати дебагер.
- **Chrome** -  [Xdebug Helper](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc?hl=en-US)
- **Firefox** - [Xdebug Helper](https://addons.mozilla.org/en-US/firefox/addon/xdebug-helper-for-firefox/)

*__Примітка__: Для того щоб з'явилась папка `code` потрібно встановити мадженту через докер, в іншому випадку папки просто не буде.*

- Після чого в **PhpStorm** потрібно зайти в `File -> Settings -> PHP -> Servers` і натиснути на **+**. 
- Цифра 1 - Кількість файлів(потрібно створити 2 файла),
- Цифра 2 - Потрібно включити мапінг що знаходиться під хостом, і обрати папку кій надамо права (`/var/www/html`),
- Цифра 3 - Назва яку потрібно задато обов'язково `magento2.local`,
- Цифра 4 - Це хост на якому буде знаходитись сам проект, теж однаковий в 2 файлах,
- Цифра 5 - Це порт підключення, в одному він має бути `80`, а в іншому `443`;

Вже після чого просто натискаєм Apply і дебагир готовий!

Підготовка та встановлення необхідних інструментів
--------------------------------------------------
### Linux
*__Примітка__: Перевірено на __Ubuntu__ та __Manjaro__ дистрибутивах, також буде працювати на усіх похідних від __Debian__ та __Arch__.*

Для роботи необхідно встановити офіційний плагін для Docker - [local-persist](https://github.com/MatchbookLab/local-persist) за допомогою команди:
`curl -fsSL https://raw.githubusercontent.com/MatchbookLab/local-persist/master/scripts/install.sh | sudo bash`

Необхідно оновити програму `docker-compose` на найновішу версію, на момент написання інструкції це **1.29.2**, для цього виконайте команди:
- `sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
- `sudo chmod +x /usr/local/bin/docker-compose`

Перевірити свою версію програми `docker-compose` можна за допомогою команди `docker-compose --version`

**Важливо оновити локальні імеджі** контейнерів, так як вони кешуються, і можуть виникати помилки при старті проекту. 
Для повторного завантаження імеджів з хмари виконайте команду `docker-compose pull` у директорії з проектом.

### MacOS
*__Примітка__: Гілка зі __збіркою для MacOS__ знаходиться у відповідній гілці репозиторію*

Для роботи потрібно встановити **Homebrew** та **Mutagen (beta)**:
- **Homebrew** - `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"`
- **Mutagen** - `brew install mutagen-io/mutagen/mutagen-beta`
- Запустити **службу Mutagen** - `mutagen daemon start`

Для роботи з Mutagen необхідно усі команди для роботи з контейнерами змінити з `docker-compose ...` на `mutagen compose ... (up, down, start, down, rm ...)`

**Mutagen** - це програма, яка дозволяє робити синхронізацію файлів між локальною файловою системою (ФС) і ФС контейнера без втрати продуктивності.

Для того, щоб перевірити статус виконання синхронізації можна ввести команду `mutagen sync monitor`

**Важливо оновити локальні імеджі** контейнері, так як вони кешуються, і можуть виникати помилки при старті проекту.
Для повторного завантаження імеджів з хмари виконайте команду `docker compose pull` у директорії з проектом.

**При виникненні помилок з не існуючим класом, або ж не можливістю створення файлу - потрібно виконати команду у контейнері PHP `chmod -R 777 app/etc/ pub/static pub/media/ var/ generated/`** 

У випадку якщо **Xdebug** не працює, перевірте значення змінної `xdebug.remote_connect_back` у файлі `config/php/php.ini`, воно має бути рівним `0` або `Off`

Реєстр (pre-build) PHP імеджів
--------------------------------------
Всі доступні версії PHP контейнерів доступні за посиланням: [`https://web.docker.pp.ua/#!taglist/magento2-php`](https://web.docker.pp.ua/#!taglist/magento2-php)

*__Примітка__: також можете переглянути репозиторій за посиланням [https://web.docker.pp.ua/](https://web.docker.pp.ua/)*

E-MAIL
------
Для листів встановлена локальна заглушка, яка не дозволить відправити лист на реальну поштову скриньку, а лише збереже його локально для перегляду.

Переглянути всі отримані листи можна за посиланням: [`http://localhost:8025/`](http://localhost:8025/)

![](https://i.imgur.com/GaarM5O.png)

SSL сертифікати
-----------------------------------------

Для коректної роботи SSL необхідно імпортувати кореневий (CA) сертифікат в браузер, для того щоб браузер міг довіряти самопідписаним сертифікатам:
- Chrome - Settings - [Privacy and security] Security - [Advanced] Manage certificates - Authorities
- натиснути кнопку Import
- ![](https://i.imgur.com/BjtlZ9X.png)
- перейти у директорію з проектом `docker-magento2/ssl/ssl_generator`
- змінити фільтр на `All Files`
- ![](https://i.imgur.com/jsJOSJR.png)
- вибрати файл `rootCA.crt`
- 
- відмітити 3 чекбокси і натиснути ОК
- ![](https://i.imgur.com/hjMKC8T.png)
- додати `0.0.0.0 sample.test` до `/etc/hosts` файлу
- готово
  - ![](https://i.imgur.com/mBxnMks.png)

Переглянути активні контейнери
------------------------------
*__Примітка__: виконуємо у локальному терміналі*

Команда **`docker ps`**

![](https://i.imgur.com/l5WV11H.png)

- `CONTAINER ID` - ID контейнера
- `IMAGE` - образ контейнера на якому він працює
- `COMMAND` - головна команда контейнера (задається при створенні в `Dockerfile`)
- `CREATED` - дата створення
- `STATUS` - час роботи від старту
- `PORTS` - показує як локальний (доступний на ПК) порт співвідноситься до порту в контейнері Docker
- `NAMES` - ім'я контейнера - використовується у командах по роботі з контейнером

CLI в контейнері
----------------
Як заходити в PHP контейнер, щоб не зіпсувати права доступу:

**`docker exec -ti docker_magento2-81_php_1 su www-data -s /bin/bash`** - за допомогою цієї команди ви відкриєте CLI контейнера і там вже виконувати усі решту маніпуляції
- `docker_magento2-81_php_1` - ім'я контейнера PHP (див. секцію [`Переглянути активні контейнери`](#переглянути-активні-контейнери))

Використання Docker-compose
---------------------------
*__Примітка__: виконуємо у локальному терміналі*

- Клонуємо GIT-репозиторій на локальний комп'ютер: **`git clone https://github.com/PaWuK11/magento2-docker.git`**
- Переходимо у директорію, яка була створена у попередньому пункті **`cd magento2-docker`**
- Запускаємо контейнери **`docker-compose up`**
- Перевіряємо статус контейнерів **`docker ps`** (у новому вікні терміналу): 
    - має бути активно декілька контейнерів (`nginx`, `php`, `mysql`, `mailhog`, `redis`, `elasticsearch`...)
    - ![](https://i.imgur.com/sJxjcgs.png)
- переходимо в контейнер PHP **`docker exec -ti docker_magento2-81_php_1 bash`**

Завантаження Magento
--------------------
*__Примітка__: виконуємо у контейнері PHP*

- за допомогою **`СOMPOSER`**
    - **`composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition`** - завантажуємо файли за допомогою Composer
    - Далі потрібно буде ввести ключі які генеруються на сайті https://marketplace.magento.com/ після реєстрації
    - Зайти в свій профіль, і далі в MarketPlace -> My Products -> Access Keys
    - Create A New Access Key якому дати довільну назву і вставити ці ключі в консоль
    - **`mv project-community-edition/{*,.*} .`** - переміщуємо файли на рівень нижче (можуть бути повідомлення про помилку - ігноруємо)
    - **`rm -rf project-community-edition`** - видаляємо директорію, вона нам більше не потрібна
    - **`chown -R www-data:www-data .`** - змінюємо права доступу після завантаження на коректні
    - **`exit`** або **'Ctrl + D'** - виходимо з терміналу контейнера но це не бажано робити на цьому етапі
 
Права доступу до директорій
---------------------------
*__Примітка__: виконуємо у локальному терміналі*

Для правильної роботи Magento необхідно надати право на запис у наступні директорії:
- `var`
- `app/etc`
- `pub/media`
- `pub/static`

Для цього необхідно виконати команду (знаходитись у директорії проекту):
- **`chmod -R 777 app/etc/ pub/static pub/media/ var/ generated/` в контейнері PHP**
- **`sudo chown -R $USER:$USER code` в локальному терміналі**

Встановлення Magento
--------------------
*__Примітка__: виконуємо у контейнері PHP*

- **`bin/magento`** - перевіряємо чи працює консоль Magento
- ![](https://i.imgur.com/jVwRPRS.png)
- **`bin/magento setup:install --db-host=magento2_mysql --db-name=magento2 --db-user=magento2 --db-password=magento2 --search-engine=elasticsearch7 --elasticsearch-host=http://magento2_elasticsearch:9200 --elasticsearch-port=9200 --elasticsearch-enable-auth=0 --session-save=redis --session-save-redis-host=magento2_redis --session-save-redis-port=6379 --session-save-redis-db=1 --cache-backend=redis --cache-backend-redis-server=magento2_redis --cache-backend-redis-port=6379 --cache-backend-redis-db=2 --admin-user=root --admin-password=magent0 --admin-email=test@example.com --admin-firstname=name --admin-lastname=surname --base-url=https://magento2.dev/ --backend-frontname=admin --language=en_US --currency=USD --timezone=America/Chicago --cleanup-database --use-rewrites=1`**
    - `db-host=magento2_mysql` - хост бази даних (БД)
    - `db-user=magento2` - користувач БД
    - `db-password=magento2` - пароль користувача БД
    - `db-name=magento2` - назва БД
    - `search-engine=elasticsearch6` - використовувати пошуковий сервер ElasticSearch6
    - `elasticsearch-host=http://magento2_elasticsearch:9200` - хост ElasticSearch
    - `elasticsearch-port=9200` - порт ElasticSearch
    - `elasticsearch-enable-auth=0` - не використовувати аутентифікацію ElasticSearch
    - `session-save=redis` - використовувати Redis для зберігання сесій
    - `session-save-redis-host=magento2_redis` - хост Redis
    - `session-save-redis-port=6379` - порт Redis
    - `session-save-redis-db=1` - БД Redis
    - `cache-backend=redis` - використовувати Redis для зберігання кешу
    - `cache-backend-redis-server=magento2_redis` - хост Redis
    - `cache-backend-redis-port=6379` - порт Redis
    - `cache-backend-redis-db=2` - БД Redis
    - `admin-user=anbis` - користувач для адмін-панелі
    - `admin-password=magent0` - пароль користувача адмін-панелі
    - `admin-email=test@example.com` - email користувача адмін-панелі
    - `admin-firstname=name` - ім'я користувача адмін-панелі
    - `admin-lastname=surname` - прізвище користувача адмін-панелі
    - `base-url=https://magento2.dev/` - URL для доступу Magento з браузера (`magento2.dev`, `magento2.local`, `magento2.test`)
    - `backend-frontname=admin` - URL-постфікс для доступу в адмін-панель
    - `language=en_US` - локаль магазину
    - `currency=USD` - валюта магазину
    - `timezone=America/Chicago` - часова зона магазину
    - `cleanup-database` - очистити базу перед встановленням Magento
    - `use-rewrites=1` - використовувати SEO-friendly URL для категорій і продуктів
- ![](https://i.imgur.com/JrApk26.png)
    
- перейти по URL, який був вказаний в параметрі `base-url` ([`https://magento2.dev/`](https://magento2.dev/))
- ![](https://i.imgur.com/RFHAAvK.png)

Використання контейнера для існуючого проекту Magento
-----------------------------------------------------
*__Примітка__: виконуємо у контейнері PHP*

- створити директорію `code` у директорії з проектом
- копіювати або перенести усі файли Magento у директорію `code`
- імортувати БД з файлу
- перевірити коректні налаштування файлу `app/etc/env.php` (файл-примірник `env.php.example`)
- якщо директорія `vendor` порожня - виконати в команду **`composer install`**
- за необхідності встановити коректні права доступу до директорій
- виконати **`bin/magento setup:upgrade`**
- **`bin/magento setup:store-config:set --base-url="http://magento2.dev/"`**
- **`bin/magento setup:store-config:set --base-url-secure="https://magento2.dev/"`**
- **`bin/magento cache:flush`**
- додати `0.0.0.0 magento2.dev` до `/etc/hosts` файлу
    
Імпорт бази даних з backup-файлу
--------------------------------
*__Примітка__: виконуємо у локальному терміналі*

**`cat backup.sql | docker exec -i CONTAINER_NAME /usr/bin/mysql -u ROOT --password=PASSWORD DATABASE`**:
- `backup.sql` - файл для імпорту
- `CONTAINER_NAME` - контейнер з MySQL (по замовчуванню `magento2_mysql`)
- `ROOT` - ім'я користувача MySQL (по замовчуванню `root`)
- `PASSWORD` - пароль користувача MySQL (по замовчуванню `magento2`)
- `DATABASE` - база данних MySQL (по замовчуванню `magento2`)

Backup (dump) бази у файл
-------------------------
*__Примітка__: виконуємо у локальному терміналі*

**`docker exec CONTAINER_NAME /usr/bin/mysqldump -u ROOT --password=PASSWORD DATABASE > backup.sql`**:
- `CONTAINER_NAME` - контейнер з MySQL (по замовчуванню `magento2_mysql`)
- `ROOT` - ім'я користувача MySQL (по замовчуванню `root`)
- `PASSWORD` - пароль користувача MySQL (по замовчуванню `magento2`)
- `DATABASE` - база данних MySQL (по замовчуванню `magento2`)
- `backup.sql` - файл для імпорту

Видалити DEFINER з dump файлу
-----------------------------
*__Примітка__: виконуємо у локальному терміналі*

**`cat backup_WITH_definers.sql | sed -e 's/DEFINER[ ]*=[ ]*[^*]*\*/\*/' > backup_WITHOUT_definers.sql`**
- `backup_WITH_definers` - файл з дефайнерами
- `backup_WITHOUT_definers` - файл для імпорту, який вже не містить дефайнерів

Комади і терміни які часто використовуються
-------------------------------------------------------
*__Примітка__: виконуємо у локальному терміналі*

Для редеплоя потрібно використати такі команди:
- bin/magento setup:upgrade
- bin/magento setup:di:compile
- bin/magento cache:flush

Для відключення 2 факторної верефікацї в адмінку, потрібно виключити цей модуль:

Варіант 1:
Перейдіть у файл `code -> app -> etc -> config.php`, і `Magento_TwoFactorAuth` замініть з `1` на `0`, після чого пропишіть `bin/magento setup:upgrade`

Варіант 2:
Пропишіть `bin/magento module:disable Magento_TwoFactorAuth` пілся чого `bin/magento setup:upgrade`
---
Вивід бази данних в PHPStorm
-------------------------------------------------------
*__Примітка__: Виконується після встановлення мадженти і налаштування самої бази*

- Натисніть на `DataBase` в правому верхньому кутку, далі `+ -> DataSource -> MySQL`
- Відкриється вікно:
- ![](https://i.paste.pics/M4KEH.png)
- Цифра 1 - Це порт, який потрібно вказати `3307`
- Цифра 2 - Це поля, де має бути `magento2`
- Цифра 3 - Це перевірка підключення, якщо ж якась помилка, то ви неправильно встановили базу, якщо ж вас повідомляє про успішне підключення, то просто нажміть `Apply`

**Обов'язково!**
Не нажимайте `Apply` без перевірки підключення
