# 10-monitoring-06-incident-management


ОБЩАЯ СВОДКА ПО ИНЦИДЕНТУ

между 22:52-21/10/18 и 23:03-22/10/18 пользователи столкнулись с отображением информации, которая была устаревшей и противоречивой. не смотря на то, что в целом никакие данные не были утеряны, в течении этого периода ГитХаб не обрабатывал вебхуки, и не позволял проведение пушей, сборок и публикаций страниц.
общая длительность аутейджа составила 24 часа 11 минут.

ВВЕДЕНИЕ

21 октября в 22:52 UTC проводились плановые работы по техническому обслуживанию для замены отказавшего оптического оборудования 100G. 

СБОЙ

Работы привели к потере связи между узлом сети Восточного побережья США и основным центром обработки данных Восточного побережья США. Связь между этими датацентрами была восстановлена через 43 секунды, но это короткое отключение вызвало цепочку событий, которые привели к 24 часам и 11 минутам деградации обслуживания.

ОБНАРУЖЕНИЕ

21 октября в 22:54 UTC внутренняя система мониторинга ГитХаба начала генерировать алерты об различных ошибках. В 23:02 UTC отвественные мониторинг инженеры установили, что топологии некоторых кластеров в состоянии сбоя.

ВОССТАНОВЛЕНИЕ

общий флоу восстановления работоспособности свелся к изменению топологии сети на верхнем уровне. для сохранности данных, все кластеры MySQL были заблокированы на запись. уже принятые транзакции позже были обработаны вручную.
при необходимости данные восстанавливались из бекапов. в моменты пиковой нагрузки, подключались дополнительные MySQL реплики, работающие в режиме только чтение.

ПРИЧИНА (РУТКОЗ)

ошибочная топология, при которой данные могли быть записаны на несколько различных серверов в статусе основных. при сбое связи каждые такие данные стали считаться уникальными, что привело к конфликту репликации данных после восстановления связи.

КОММУНИКАЦИЯ

ГитХаб информировал пользователей о инциденте через объявление на сайте, а так же публикациями в соц сетях

ДЕЙСТВИЯ ПОСЛЕ ИНЦИДЕНТА

ГитХаб предпринял ряд технических инициатив, таких как:
1. настройка конфигурации Оркестратора
2. миграция в новый механизм сообщений о статусе серверов
3. изменение логической топологии сети, путем создания избыточных дата центров, что позволит выдержать полный отказ одного центра обработки данных без воздействия на пользователя.
4. и так далее
А так же организационных инициатив, которые помогут в дальнейшем предотвратить повторение подобных ситуаций.
