## Задание:

# Test for Pest

The department for disease control wants to offer a system to safely and securely handle test results nationwide.

Users: thousands of test operators, millions of customers to be tested, hundreds of government officials, thousands of doctors.

Requirements:

* Customers can schedule a test appointment with a local test operator.

* Test operators can verify the customers' appointments and personal data on the spot.

* Test operators can submit the test result.

* Customers can view their test result.

* The system can handle tests for different diseases.

* Local government officials can view the test statistics for their area of responsibility.

* Department for disease control can view local and nationwide statistics with different prediction models.

* Local government can contact customers if they submitted an email address or phone number.

* Doctors can view the customer's test history if customer enabled them.

Additional Context:

* The system must be able to react quickly on outbreaks of new diseases and therefore the availability of new test types.

* Tests are carried out workdays during office hours.

* The system will potentially be exported to other countries.

* You have a contract with the government so money is not important.

[](https://www.architecturalkatas.com/kata.html?kata=TestPest.json)


## Выполнение:

### Отсев:
В данном случае у нас фактически неограниченные ресурсы – взаимодействие с государством.

Можем немного изменить требование по просмотру результатов: в реалиях РФ существуют ЕМИАС, ЭМК и прочие сервисы, которые позволяют просматривать результаты исследований – данные можно просто выгружать туда. Это касается просмотра информации со стороны врачей, пациентов и Роспотребнадзора (Department for Disease Control). Такой подход значительно упрощает проектирование с точки зрения расчёта пользовательской нагрузки, так как ЭМК уже берёт на себя эту задачу.

#### Итог: Сокращаем требование просмотра результатов до выгрузки в систему электронных медицинских карт.

### Scope refinement:

Система работает как прокси между ЭМК и операторами тестирования. Она хранит только обезличённые данные по тестированию. Данными, полученными в результате исследований и привязанными к личности граждан, можно управлять только временно – до отправки в ЭМК. Запись на тестирование производится через ЕПГУ или ЕМИАС.

Приходим к выводу, что система должна состоять из следующих частей:

##### API операторов тестирования:

• Операторы тестирования могут получать списки пациентов, для которых необходимо провести тестирование.  
• Операторы тестирования могут загружать результаты анализов в систему.  
• Все запросы подписываются ЭЦП.  
• Взаимодействие осуществляется именно через API, так как операторы тестирования могут быть очень крупными (например, «Инвитро» и другие организации).

##### Личный кабинет органа власти (WEB + REST API):

• Орган власти может добавлять операторов тестирования (юридические лица – медицинские организации).  
• Органы власти могут добавлять виды тестирования.  
• Органы власти могут просматривать обезличённую статистику по результатам исследований.

### Требования:

#### Функциональные:

• Авторизация для различных групп пользователей через ЕСИА.  
• Возможность добавления видов анализов.  
• Возможность добавления операторов тестирования.  
• Просмотр статистики по регионам.  
• Возможность получения информации о назначенных исследованиях.  
• Возможность отправки результатов тестирования.  
• Возможность выгрузки данных в ЭМК (электронные медицинские карты).

#### Нефункциональные:

• Обработка персональных данных согласно №152-ФЗ.  
• Соблюдение гайдлайнов интерфейсов госуслуг для личного кабинета с веб-интерфейсом ([https://guides.gosuslugi.ru/](https://guides.gosuslugi.ru/)).
### Ограничения:

• В процессе разработки можно использовать только opensource-решения с лицензиями MIT, LGPL.  
• Для взаимодействия между сервисами используется формат данных XML и протокол SOAP.

### Расчёт нагрузки:

Выполняем расчёт для самых высконагруженных частей системы. ЛК для органов власти не является высконагруженной частью приложения, достаточно обеспечить возможность обработки до 100 запросов в минуту.
#### Приложение:

Во время пандемии SARS-CoV-2 в 2021 году проводилось около 360 тыс. исследований в сутки ([http://static.government.ru/media/covid19/reports/QuRFFLxLwTAqlwb2r1LslluE7fQFWRgg/2021-08-25_coronavirus_government_report.pdf](http://static.government.ru/media/covid19/reports/QuRFFLxLwTAqlwb2r1LslluE7fQFWRgg/2021-08-25_coronavirus_government_report.pdf)). Для обеспечения надежности работы можно заложить коэффициент 2× для количества проводимых исследований.

Таким образом, нагрузка на API операторов тестирования может достигать 720 тыс. запросов в сутки, что примерно составляет 90 тыс. запросов в час в течение рабочего дня.

#### БД:

Наиболее нагружённые таблицы:

1. Пациенты  
     Население РФ составляет 140 млн. человек. Применив коэффициент роста 1.25×, получаем около 175 млн. записей.
2. Проведённые исследования  
     На пике – возможно около 720 тыс. записей в сутки.
3. Записи на исследования  
     На пике – возможно около 720 тыс. записей в сутки.