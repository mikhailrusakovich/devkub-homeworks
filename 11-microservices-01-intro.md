# Домашнее задание к занятию "11.01 Введение в микросервисы"

## Задача 1: Интернет Магазин

Руководство крупного интернет магазина у которого постоянно растёт пользовательская база и количество заказов рассматривает возможность переделки своей внутренней ИТ системы на основе микросервисов. 

Вас пригласили в качестве консультанта для оценки целесообразности перехода на микросервисную архитектуру. 

Опишите какие выгоды может получить компания от перехода на микросервисную архитектуру и какие проблемы необходимо будет решить в первую очередь.

---

Выгоды:
1. Сниженная зависимость между компонентами системы
2. Предполагает оперативное наращивание мощностей за счет горизонтального масштабирования, 
   и позволяет сокращать объем мощностей системы при снижении нагрузки
3. Повышается работоспособность системы, за счет снижения рисков деградации системы и полного отказа из-за отсутствия единой точки отказа
4. Возможность использования разных технологий в разных сервисах, а так же применение оптимального стека технологий, для новых сервисов. 
5. Возможность гибко добавлять сложный функционал за счет разработки новых сервисов
6. Повышение возможности делать более частые релизы, т.е. быстрее выводить новые функции,
   сюда же можно отнести возможность автоматизации CI/CD 
   и ускорение тестирования - тестирование отдельных сервисов, а не монолитной системы регресс тестированием при модификации какой-либо части
7. В теории за счет повторения сервисами структуры коммуникации организации, можно создать более гармоничную систему, 
   при разделении команд на разных сервисах с разными задачами и бизнес целями. Что может дать вполне оптимальный результат (Закон Конвея)
   
Проблемы:
1. Микросервисы не являются панацеей, и решать проблемы вычислительных мощностей и инфраструктуры так же по-прежнему придется.
2. Необходимо найти баланс, и не дробить систему слишком, чтобы не усложнить систему слишком мелкими сервисами.
3. Необходимо развивать дополнительные компетенции службы технической поддержки и сопровождения.
4. Так же необходимо повышать квалификацию разработчиков и функциональных обязанностей (документирование, версионирование), 
   возможно придется расширить штат разработчиков в связи с распределением систем и разделением команд для разных сервисов.
5. Введение дополнительной службы (возможно внтурикомандного распределение обязанностей) - DevOps


### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
