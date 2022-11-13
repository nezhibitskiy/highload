# Netflix

## Расчет целевой аудитории

| Целевая аудитория                                 | Мир       |
|---------------------------------------------------|-----------|
| Размер ЦА (без учета нелегальных юзеров)          | 220 млн   |
| Размер ЦА (с учетом нелегальных юзеров)           | ~ 300 млн |
| Количество посетителей в сутки                    | 51.2 млн  |
| Используемые данные подписчиком в час (в среднем) | 3 Гб      |
| Среднее время просмотра                           | 3.2 часа  |

Источник: https://www.comparitech.com/blog/vpn-privacy/netflix-statistics-facts-figures/


| Распределение аудитории по миру   |           |
|-----------------------------------|-----------|
| Соединенные Штаты Америки         | 74.58 млн |
| Европа, Центральная Азия, Африка  | 73.73 млн |
| Латинская Америка                 | 39.61 млн |
| Азиатско-Тихоокеанский регион     | 33.72 млн |

Источник: https://www.demandsage.com/netflix-subscribers/ 

## Расчет нагрузки

Поскольку системы такого масштаба, как правило, разделены на регионы, рассмотрим применимый для нас регион, а именно - Европа, Центральная Азия, Африка.

Ежедневно Netflix нашего региона посещают 12.58 млн человек. Учитывая среднее время просмотра (3.2 часа, с учетом просмотра трейлеров), ежедневно на Netflix просматривают 40.3 млн часов.

| Средний вес 1 часа видео  | Гб  |
|---------------------------|-----|
| FullHD                    | 1.5 |
| 2K                        | 3   |
| 4K                        | 7   |

Исходя из среднего веса 1 часа видео и учетом того, что современные экраны устройств имеют высокое разрешение, а также то, что Netflix предоставляет свои фильмы в 4К разрешении, примем средний вес 1 часа - 3 Гб.
### Таким образом ежедневная нагрузка на сервис составит 120.8 млн Гб трафика = 115,2 Пб

Загрузка статики: главная страница Netflix ориентировочно весит 3 Мб, страница с поиском - 6 Мб (6 запросов от одного пользователя), страница с подборками - 4 Мб, а страница с профилем - 2 Мб. Подразумевается, что подборки меняются раз в 2-3 дня. Предположим, что каждый юзер будет заходить на все эти страницы каждый день. Учтем, что пользователь так же будет загружать превью фильмов и сериалов. Поскольку превью имеют малое разрешение (поскольку карточка фильма на странице с подборками маленькая), вес каждого превью составит ~10Мб. Пусть пользователь за день загружает 12 превью (превью загружается не у всех фильмов на странице, а только у тех, на которые пользователь навел мышкой и удерживал там её некоторое время). Таким образом, ещё 120 Мб трафика. Загрузка будет происходить 1 раз, после чего данные о страницах будут храниться в кэше пользователя. Таким образом, ежедневно будет скачиваться: 12,58 * (3+6+4+4+120) = 1723.5 млн Мб данных = 1.6 Пб 

Расчитаем нагрузку в пиковые часы. Предположим, что наиболее нагруженным временем работы Netflix считается 20:00 GMT (+1 час для центральной части европы  - 21:00). В это время заходит 50% от общего числа пользователей в день. Рассмотрим ситуацию выпуска новой серии популярного сериала в это время. В таком случае к сервису может подключиться ещё 20% пользователей относительно ежедневного траффика. Таким образом расчетная пиковая нагрузка составит 70% от ежедневного числа пользователей, что составит 8.8 млн пользователей.

| Требуемая скорость для скачивания видео  | Мбит/с  |
|------------------------------------------|---------|
| FullHD                                   | 5-8     |
| 2K                                       | 25      |
| 4K                                       | 45      |

Для расчетов будем использовать среднюю скорость подключения в 6 Мбит/с. Таким образом, общая скорость по сети должна составлять 52.8 млн Мбит/с = 50,4 Тбит/с

Для расчета RPS предположим, что в среднем каждый пользователь посещает 5 уникальных ссылок со статикой (как в расчете на статику) и ещё 10 запросов без загрузки статики. Получаем RPS на бэкенд = 12.58 * 1 000 000 * 15 / (24 * 60 * 60) = 2184 RPS

Для расчета пикового RPS на бэкенд предположим, что 70% пользователей за 1 час совершили по 5 запросов 
8.8 * 1 000 000 * 5 / (60 * 60) = 12222.2 RPS

| RPS          |         |
|--------------|---------|
| Стандартный  | 2184    |
| Пиковый      | 12222.2 |

### Требуемый размер хранилища

На Netflix хранится около 36000 часов фильмов и сериалов. Учитывая то, что каждый из фильмов хранится в 50 экземплярах с разным качеством видео, аудио, частотой кадров и т.д., расчитаем размер общего хранилища под фильмы. Размер часа FullHD видео в среднем составляет 9 Гб, 4К - 29 Гб. Таким образом, каждый час видео на Netflix ориентировочно занимает 65 Гб памяти (с учетом превью и трейлера). Общее количество памяти: 2.2 Пб данных.
Поскольку на данном сервисе нет большого количества данных пользователей, табличные данные будут занимать менее 1% от общей памяти сервиса.

## Логическая схема БД

![image](https://user-images.githubusercontent.com/71338063/201523548-9f9ac44f-49de-4c7e-a395-4f49aabaee7c.png)

Примерный расчет размера таблиц:

Movie/series: 4 + 128 + 128 + 256 + 4 + 4 + 1 + 1024 + 128 + 128 + 4 + 8 = 1817 -> 1817 * 4000 = 7268000 байт ~ 7 Мб (суммарно 4000 разных фильмов и сериалов)

User: 4 + 128 + 128 + 128 + 128 + 4 + 128 = 648 -> 648 * 73 730 000 = 47 777 040 000 байт ~ 44.45 Гб

Rating: 4 + 4 + 4 + 1 = 13 байт -> 13 * 10 * 73 730 000 = 9 584 900 000 байт ~ 8.93 Гб (среднее количество отзывов пользователя - 10)

Likes: 4 + 4 + 4 = 12 байт -> 12 * 15 * 73 730 000 = 13 271 400 000 байт ~ 12.36 Гб (среднее число избранных фильмов/сериалов - 15)

Season: 4 + 4 + 4 + 128 = 140 -> 140 * 3 * 4000 = 1 680 000 байт = ~1.6 Мб (в среднем по 3 сезона на сериал)

Episode: 4 + 128 + 2 + 1024 + 4 + 128 + 128 = 1418 -> 1418 * 10 * 3 * 4000 = 170 160 000 байт ~ 162 Мб (и по 10 серий в сезоне)

Staff: 4 + 4 + 4 + 2 = 14 -> 14 * 20 * 4000 = 1 120 000 байт ~ 1.07 Мб (В среднем фиксируем 20 актеров в каждом сериале)

Genres: 4 + 4 + 2 = 12 -> 12 * 5 * 4000 = 240 000 байт ~ 0.23 Мб (по 5 жанров у каждого фильма/сериала)

Countries: 4 + 4 + 2 = 12 -> 12 * 2 * 4000 = 96 000 байт ~ 0.09 Мб (и по 2 страны)

Person: 4 + 128 + 128 + 1024 = 1284 -> 1284 * 20 000 = 25 680 000 байт ~ 24.5 Мб (20 000 страниц актеров)

Photos: 4 + 128 = 132 -> 132 * 2 * 20 000 = 5 280 000 байт ~ 5.04 Мб (у каждого актера, в среднем, по 2 фотографии)

Название стран и жанров считать не имеет смысла из-за малости их размера
Таким образом, получаем: 
Пользовательские данные: ~65,74 Гб
Контент: ~0.19 Гб

Сведения про session_id будем хранить в редисе, где user_id - ключ, а значение - id сессии (64 байта). Таким образом, храним: (4 + 64) * 73 730 000 = 4.67 Гб

## Физическая схема
### Архитектура

![image](https://user-images.githubusercontent.com/71338063/201527540-d5ec816b-0922-4bea-8fe9-41ff98c8434d.png)

### Индексы в таблицах

Для ускорения поиска данных о фильмах применим хэш индекс на ID фильма. Для ускорения загрузки основных сведений о фильме, сделаем этот индекс покрывающим, включив в него название фильма и остальные данные, которые требуется вывести на стандартной карточке фильма. 
Для таблицы пользователей, рейтингов и лайков сделаем хэш индекс по ID пользователя. По ID фильма делать индекс не стоит, поскольку можно заметить, что таблица фильмов денормализована и содержит сведения о количестве оценок и об их сумме. В случае, если потребуется выводить на сайт количество лайков пользователей (добавлений в избранное), следует добавить поле в таблицу с фильмами.
В таблицу с сезонами и эпизодами также следует добавить хэш индекс на ID сериала и ID сезона соответственно.
Для таблиц staff, genre и countries следует добавить индексы на filmID. На таблицу с фотографиями актеров следует добавить индекс на ID актера.  


### Шардирование и репликация
Сведения о контенте не имеет смысла шардировать, поскольку данных в таблицах содержится не так много. Имеет смысл реплицировать для надежности и отказоустойчивости в случае поломки БД. Для БД с контентом будет создаваться три реплики.

Сведения о пользователях можно шардировать по ID пользователя, таким образом распределив нагрузку на несколько серверов (например, имея 10 шардов). Данные также можно реплицировать для обеспечения отказоустойчивости и надежности. В момент логина можно посылать запрос во все шарды и получать данне об ID пользователя.
 
### CDN сервера

Для снижения latency и нагрузки на сервера, будем использовать CDN сервера, на которых будем хранить контент. Располагать их будем вблизи больших городов. Расположим сервера в соответствии с картой:

![image](https://user-images.githubusercontent.com/71338063/201527893-a3181547-7293-490d-b45c-fb74196afe25.png)

Загрузка видео с CDN-сервера будет происходить с помощью VoD-трансляции (video on demand), при котором контент заранее подготовлен и содержит длительность контента, а также полную информацию о кодеке, аудиодорожку и субтитры. Взаимодействие пользователя с CDN-сервером происходит сессионно - контент отдается сегментами в буфер пользователю. В данной схеме необходимо обеспечить бесшовное переключение между серверами при разрыве сессии или при смене одного хоста на другой.

Будем использовать 2 различные конфигурации CDN серверов, один из которых будет предназначен для популярного контента, а другой - для остального контанта. 

Конфигурация CDN сервера:

Процессор: 2 х Intel Xeon® Platinum 8380 (80x2.4 ГГц HT)

Оперативная память: 512 Гб

Сеть: 

* Для сервера с общим контентом - 1 сетевая карта по 100 Гбит/сек
* Для сервера с популярным контентом - 2 сетевые карты по 100 Гбит/сек

Количество: 

* Сервера с общим контентом - 30% * 51562 Гбит/сек / 100 = 155 * 2 (по реплике на каждый) = 310
* Сервера с популярным контентом - 70% * 51562 Гбит/сек / (100 * 2) = 180 * 2 = 360

Память:

  * Сервера с общим контентом - 2.2 Пб. Будем хранить весь основной контент в каждой точке, так как он занимает 2 285,16 Тб можно ставить. Максимальная конфигурация сервера - 90 слотов под жесткие диски. Возьем 90 SSD по 4 Тб: 2 285,16 / 360 = 7 файловых серверов. Для отказоустойчивости можно сделать по 1й реплике на каждый CDN с крупным контентом.
  * Сервера с популярным контентом - 5% * 2.2 Пб = 112,64 Тб. Возьмем 30 SSD по 4 Тб и получим: 112,64 / (30 * 4) = 1 сервер. Дополнительно для хранения копий контента в репликах будем использовать еще 2 серверов. Будем использовать каждую реплику при отдаче контента, чтобы распределять самый популярный контент и трафик пользоваетля между ними.


Оригиналы видео храним отдельно. 2 285.16 Тб распределим на 16 серверов - их можно хранить на HDD, объединенных в RAID массивы

## Выбор технологий

Для бэкенда будем использовать микросервисную архитектуру, написанную на Golang для обеспечения эффективной ассинхронной работы. В межсервисном взаимодействии будем использовать envoy для балансировки. Все взаимодействие будет происходить посредством grpc. 

Для фронтенда 

## Схема проекта

