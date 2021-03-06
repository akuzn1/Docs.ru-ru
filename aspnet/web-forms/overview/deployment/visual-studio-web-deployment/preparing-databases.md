---
uid: web-forms/overview/deployment/visual-studio-web-deployment/preparing-databases
title: "ASP.NET веб-развертывания с помощью Visual Studio: Подготовка к развертыванию базы данных | Документы Microsoft"
author: tdykstra
description: "Этот учебник ряд показано развертывание ASP.NET (публикации) веб-приложения для веб-приложениях службы приложений Azure или стороннего поставщика услуг размещения, Пол..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: ae4def81-fa37-4883-a13e-d9896cbf6c36
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/preparing-databases
msc.type: authoredcontent
ms.openlocfilehash: caa79725ede320c4bd3e87ac246966c57175eb8e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-web-deployment-using-visual-studio-preparing-for-database-deployment"></a>ASP.NET веб-развертывания с помощью Visual Studio: Подготовка к развертыванию базы данных
====================
По [Tom Dykstra](https://github.com/tdykstra)

[Загрузите начальный проект](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Этот учебник ряд показано развертывание ASP.NET (публикации) веб-приложения для веб-приложениях службы приложений Azure или стороннего поставщика услуг размещения, с помощью Visual Studio 2012 или Visual Studio 2010. Сведения о последовательности см. в разделе [в первом учебнике ряда](introduction.md).


## <a name="overview"></a>Обзор

Этот учебник показывает способ получения проекта готово к развертыванию базы данных. Структура базы данных, а также некоторые (но не всех) данных в двух приложения баз данных должны быть развернуты в тест, промежуточной и производственной сред.

Как правило при разработке приложения, введите тестовые данные в базу данных, не требуется для развертывания на действующем сайте. Тем не менее может также потребоваться некоторых рабочих данных, которую требуется развернуть. В этом учебнике будет настроить проект университета Contoso и подготовить скрипты SQL правильные данные при развертывании.

Напоминание: Если вы получаете сообщение об ошибке или что-то не работает, как пройти учебник, не забудьте проверить [страницу устранения неполадок](troubleshooting.md).

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

В примере приложения используется SQL Server Express LocalDB. SQL Server Express — бесплатный выпуск SQL Server. Она часто используется во время разработки, так как она основана на одном ядре СУБД как полной версии SQL Server. Можно проверить с помощью SQL Server Express и быть уверенным, что приложение будет вести себя таким же, в рабочей среде, за некоторыми исключениями для компонентов, которые отличаются в различных выпусках SQL Server.

LocalDB — это режим выполнения специальных SQL Server Express, предназначенное для работы с базами данных как *.mdf* файлов. Как правило, хранятся файлы базы данных LocalDB в *приложения\_данные* папку веб-проекта. Функции пользовательского экземпляра в SQL Server Express позволяет также работать с *.mdf* файлов, но функции пользовательского экземпляра рекомендуется; таким образом, рекомендуется использовать LocalDB для работы с *.mdf* файлов.

Обычно SQL Server Express не используется для рабочей веб-приложений. LocalDB в частности не рекомендуется для использования в рабочей среде веб-приложению, так как он не предназначен для работы со службами IIS.

В Visual Studio 2012 LocalDB устанавливается по умолчанию вместе с Visual Studio. В Visual Studio 2010 и более ранних версиях SQL Server Express (без LocalDB) устанавливается по умолчанию вместе с Visual Studio; то есть почему он установлен как один из необходимых компонентов в [в первом учебнике этой серии](introduction.md).

Дополнительные сведения о выпусках SQL Server, включая LocalDB, см. следующие ресурсы [работа с базами данных SQL Server](../../../../whitepapers/aspnet-data-access-content-map.md#sqlserver).

## <a name="entity-framework-and-universal-providers"></a>Entity Framework и универсальные поставщики

Для доступа к базе данных приложение Contoso университета требуется следующее программное обеспечение, должны быть развернуты вместе с приложением, так как он не включен в .NET Framework:

- [Универсальные поставщики ASP.NET](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) (включает систему членства ASP.NET для использования базы данных SQL Azure)
- [Entity Framework](https://msdn.microsoft.com/library/gg696172.aspx)

Так как это программное обеспечение включаются в пакеты NuGet, проект уже настроен, чтобы нужные сборки развертываются вместе с проектом. (Эти ссылки указывают на текущие версии этих пакетов, которые могут быть более новой версии установленных в начальный проект, загруженный в этом учебнике).

При развертывании стороннего поставщика услуг размещения вместо Azure, необходимо использовать Entity Framework 5.0 или более поздней. Более ранних версиях Code First Migrations необходим полный уровень доверия, и большинство поставщиков услуг размещения будет выполняться приложение со средним уровнем доверия. Дополнительные сведения о со средним уровнем доверия см. в разделе [развертывание в IIS в тестовой среде](deploying-to-iis.md) учебника.

## <a name="configure-code-first-migrations-for-application-database-deployment"></a>Настройка миграции Code First для развертывания базы данных приложения

База данных приложения Contoso университета управляется Code First и развернете с помощью Code First Migrations. Обзор развертывания базы данных с помощью Code First Migrations см. в разделе [в первом учебнике этой серии](introduction.md).

При развертывании базы данных приложения, обычно не развертываются просто базы данных разработки все данные в них в рабочей среде, так как большая часть данных в ней, возможно, присутствует только для целей тестирования. Например учащихся в тестовую базу данных, называются вымышленной. С другой стороны часто невозможно развернуть только структуру базы данных с данными, в его вообще. Некоторые данные в тестовой базы данных может быть реальными данными и должны присутствовать при пользователи начнут использовать приложение. Например вашей базе данных может быть таблица, содержащая допустимый уровень значения или имена реальных отдела.

Для имитации этом распространенном сценарии, следует настроить Code First Migrations `Seed` метод, который вставляет в базе данных только данные, которые должны быть существует в рабочей среде. Это `Seed` метод не должен Добавление тестовых данных, поскольку он будет выполняться в рабочей среде после Code First создает базу данных в рабочей среде.

В более ранних версиях Code First до выпуска миграции было часто `Seed` методы для вставки тестовые данные также, поскольку при каждом изменении модели во время разработки базы данных было полностью удалить и повторно создать с нуля. Code First Migrations, тестирования, данные сохраняются после изменения базы данных, поэтому включив тестовых данных в `Seed` метод не требуется. Загруженный проект использует метод, в том числе все данные в `Seed` метода инициализации класса. В этом учебнике предстоит отключить инициализатор класса и `enable Migrations. Then you'll update the `начального значения "метод в конфигурации миграции класса, чтобы он вставляет только данных, которую требуется вставить в рабочей среде.

На следующей схеме показана схема базы данных приложения:

[![School_database_diagram](preparing-databases/_static/image2.png)](preparing-databases/_static/image1.png)

Для этих учебников вы Предположим, что `Student` и `Enrollment` таблицы должно быть пустым, когда узел сначала развертывается. Другие таблицы содержат данные, должен ли предварительная загрузка приложения станет активным.

### <a name="disable-the-initializer"></a>Отключить инициализатора

Так как вы будете использовать Code First Migrations, не нужно использовать `DropCreateDatabaseIfModelChanges` Code First инициализатора. Код для этого инициализатора находится в *SchoolInitializer.cs* файл в проекте ContosoUniversity.DAL. Параметр в `appSettings` элемент *Web.config* файла вызывает этот инициализатор для выполнения каждый раз, когда приложение пытается получить доступ к базе данных в первый раз:

[!code-xml[Main](preparing-databases/samples/sample1.xml?highlight=3)]

Откройте приложение *Web.config* файл и удалите или закомментируйте `add` элемент, который задает код первый класс инициализатора. `appSettings` Элемента теперь выглядит следующим образом:

[!code-xml[Main](preparing-databases/samples/sample2.xml)]

> [!NOTE]
> Другой способ указать инициализатор класса является выполняется вызовом метода `Database.SetInitializer` в `Application_Start` метод в *Global.asax* файла. При включении миграции в проекте, который использует этот метод для указания инициализатор, удалите эту строку кода.


> [!NOTE]
> Если вы используете Visual Studio 2013, добавить следующие шаги между шаги 2 и 3: Введите PMC (a) в «entityframework пакет обновления-версии 6.1.1» для получения текущей версии EF. Затем (b) выполните построение проекта, чтобы получить список ошибок сборки и их исправления. Удалите операторы using для пространства имен, которые больше не существует, щелкните правой кнопкой мыши и выберите разрешение, чтобы добавить с помощью инструкций, где они нужны и измените вхождения System.Data.EntityState System.Data.Entity.EntityState.


### <a name="enable-code-first-migrations"></a>Включить Code First Migrations

1. Убедитесь, что проект ContosoUniversity (не ContosoUniversity.DAL) задано как автозагружаемый проект. В **обозревателе решений**, щелкните правой кнопкой мыши проект ContosoUniversity и выберите **Назначить запускаемым проектом**. Code First Migrations просмотрит Автозагружаемый проект, чтобы найти строку подключения базы данных.
2. Из **средства** меню, нажмите кнопку **диспетчер пакетов библиотеки** (или **диспетчера пакетов NuGet**) и затем **консоль диспетчера пакетов**.

    ![Selecting_Package_Manager_Console](preparing-databases/_static/image3.png)
3. В верхней части **консоль диспетчера пакетов** окна выберите ContosoUniversity.DAL в качестве проекта по умолчанию, а затем at `PM>` строке введите «enable-migrations».

    ![команду Enable-migrations](preparing-databases/_static/image4.png)

    (Если появляется ошибка *enable-migrations* команда не распознается, введите команду *EntityFramework пакета обновления — переустановить* и повторите попытку.)

    Эта команда создает *миграций* папки в проект ContosoUniversity.DAL и помещает в этой папке двух файлов: *Configuration.cs* файл, который можно использовать для настройки, а миграция *InitialCreate.cs* файл для первой миграции, который создает базу данных.

    ![Миграция папки](preparing-databases/_static/image5.png)

    Вы выбрали проект DAL в **проекта по умолчанию** раскрывающегося списка **консоль диспетчера пакетов** из-за `enable-migrations` команда должна выполняться в проект, содержащий Code First класс контекста. После этого класса в проект библиотеки классов Code First Migrations ищет строку подключения базы данных в запускаемым проектом решения. Веб-проекта в решении ContosoUniversity был задан как запускаемый проект. Если вы не хотите назначить проекту, который имеет строку подключения как автозагружаемый проект в Visual Studio, можно указать автозагружаемого проекта в команду PowerShell. Чтобы просмотреть синтаксис команды, введите команду `get-help enable-migrations`.

    `enable-migrations` Команды автоматически создается первой миграции, так как база данных уже существует. Альтернативой является возможность создать базу данных миграции. Чтобы сделать это, используйте **обозревателя серверов** или **обозреватель объектов SQL Server** удалить базу данных ContosoUniversity, прежде чем включить миграцию. После включения миграции создайте первой миграции вручную, выполнив команду «Добавить миграции InitialCreate». Затем можно создать базу данных, введя команду «update-database».

### <a name="set-up-the-seed-method"></a>Настройка Seed-метод

В этом учебнике вы добавите основных данных путем добавления кода в `Seed` метод Code First Migrations `Configuration` класса. Code First Migrations вызовы `Seed` метод после каждой миграции.

Поскольку `Seed` метод выполняется после завершения каждой миграции, уже существует данных в таблицах после первой миграции. Чтобы решить эту проблему, вы будете использовать `AddOrUpdate` метод для обновления строки, которые уже были вставлены или вставить их, если они еще не существуют. `AddOrUpdate` Метод может оказаться самым подходящим для вашего сценария. Дополнительные сведения см. в разделе [Будьте внимательны с EF 4.3 AddOrUpdate метод](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/) Джули Лерман блога.

1. Откройте *Configuration.cs* и замените комментарии в `Seed` метод следующим кодом:

    [!code-csharp[Main](preparing-databases/samples/sample3.cs)]
2. Ссылки на `List` имеют красная волнистая линия под их, так как отсутствует `using` инструкции еще для пространства имен. Щелкните правой кнопкой мыши один из экземпляров `List` и нажмите кнопку **Разрешить**, а затем нажмите кнопку **using System.Collections.Generic**.

    ![Разрешить с помощью инструкции](preparing-databases/_static/image6.png)

    Следующий код добавляет этот выбор меню `using` инструкции в верхней части файла.

    [!code-csharp[Main](preparing-databases/samples/sample4.cs)]
3. Нажмите клавиши CTRL-SHIFT-B для сборки проекта.

Теперь проект готов к развертыванию *ContosoUniversity* базы данных. После развертывания приложения, при первом запуске и перехода к странице, которая обращается к базе данных Code First будет создать базу данных и запуска этого `Seed` метод.

> [!NOTE]
> Добавление кода для `Seed` метода — один из многих способов, которые можно вставить основных данных в базу данных. Альтернативным вариантом является добавление кода для `Up` и `Down` методов класса каждой миграции. `Up` И `Down` методы содержат код, который реализует изменения в базе данных. Вы увидите их в примеры [развертывание обновления базы данных](deploying-a-database-update.md) учебника.
> 
> Также можно написать код, который выполняет инструкции SQL с помощью `Sql` метод. Например, если добавление столбца бюджета таблица Department, необходимо инициализировать все бюджеты отделов для 1000,00 долларов в процессе миграции удалось добавить соответствовать строку кода для `Up` метод для этой миграции:
> 
> `Sql("UPDATE Department SET Budget = 1000");`


## <a name="create-scripts-for-membership-database-deployment"></a>Создавать сценарии для развертывания базы данных членства

Приложение университета Contoso использует проверки подлинности системы и форм членства ASP.NET для проверки подлинности и авторизации пользователей. **Обновление кредиты** страница доступна только для пользователей, которым назначена роль администратора.

Запустите приложение и нажмите кнопку **курсы**, а затем нажмите кнопку **обновление кредиты**.

![Нажмите кнопку обновления кредиты](preparing-databases/_static/image7.png)

**Вход** появится страница, так как **обновление кредиты** страницы требуются права администратора.

Введите *администратора* имени пользователя и *devpwd* как пароль и нажмите кнопку **входа**.

![Страница входа в](preparing-databases/_static/image8.png)

**Обновление кредиты** появится страница.

![Страница «обновление» кредиты](preparing-databases/_static/image9.png)

Данные пользователя и роли находятся в *aspnet ContosoUniversity* базы данных, который задается параметром **DefaultConnection** строку подключения в *Web.config* файла.

Эта база данных не находится под управлением Entity Framework Code First, поэтому ее невозможно использовать миграции для развертывания. Поставщик dbDacFx будет использоваться для развертывания схемы базы данных, и вы настроите профиль публикации для выполнения сценария, который будет вставлять исходные данные в таблицы базы данных.

> [!NOTE]
> Система членства ASP.NET (теперь называется ASP.NET Identity) появилась в Visual Studio 2013. Новая система позволяет поддерживать приложения и таблицы членства в той же базе данных и можно использовать для развертывания и миграции Code First. Образец приложения использует ранее система членства ASP.NET, который не удается развернуть с помощью Code First Migrations. Процедуры для развертывания этой базы данных членства применяются также для любого сценария, в котором в приложении необходимо развернуть базу данных SQL Server, не могут быть созданы с Entity Framework Code First.


Здесь также обычно необходимо исключить те же данные, у вас есть разработки в рабочей среде. При развертывании узла в первый раз, чаще всего для исключения большинство или все учетные записи пользователей, созданные для тестирования. Таким образом, загружаемый проект содержит две базы данных членства: *aspnet ContosoUniversity.mdf* с пользователями, разработки и *aspnet ContosoUniversity-Prod.mdf* с пользователями в рабочей среде. В этом учебнике имена одинаковы в обеих базах данных: *администратора* и *nonadmin*. Пароль у обоих пользователей *devpwd* в базе данных разработки и *prodpwd* рабочей базы данных.

Используется для развертывания пользователи разработки в тестовой среде и конечных пользователей в промежуточной и производственной сред. Для этого вы создадите два скрипта SQL в этом учебнике, для разработки и для рабочей среды, и на следующих занятиях рассматривается следует настроить процесс публикации, для их запуска.

> [!NOTE]
> База данных членства сохраняет хэш паролей учетных записей. Чтобы развернуть учетные записи с одного компьютера на другой, необходимо убедиться, что хэширования подпрограммы не создавать разные хэши на конечном сервере не на исходном компьютере. Генераторы будут получать одинаковые хэш-коды при использовании универсальные поставщики ASP.NET до тех пор, пока не будет изменять алгоритм по умолчанию. По умолчанию алгоритм HMACSHA256 и определен в **проверки** атрибут  **[machineKey](https://msdn.microsoft.com/library/system.web.configuration.machinekeysection.aspx)**  в файле Web.config.


Можно создать скрипты развертывания данных вручную, с помощью SQL Server Management Studio (SSMS) или с помощью стороннего средства. В остальной части этого учебника будет показано, как это можно сделать в SSMS, но если вы не хотите установить и использовать SSMS можно получить скрипты из полную версию проекта и перейдите к разделу, где они хранятся в папке решения.

Чтобы установить SSMS, установите его из [центра загрузки Майкрософт: Microsoft SQL Server 2012 Express](https://www.microsoft.com/download/details.aspx?id=29062) , щелкнув [ENU\x64\SQLManagementStudio\_x64\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe) или [ ENU\x86\SQLManagementStudio\_x86\_ENU.exe](https://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe). При выборе тот для вашей системы его не удастся установить, и вы можете попробовать использовать другой.

(Обратите внимание, что для загрузки размером 600 мегабайт. Он может занять много времени для установки и требуют перезагрузки компьютера.)

На первой странице Центр установки SQL Server, нажмите кнопку **автономная установка нового SQL Server или добавление компонентов к существующей установке**и следуйте инструкциям, принимающий параметры по умолчанию.

### <a name="create-the-development-database-script"></a>Создание скрипта базы данных разработки

1. Запустите SSMS.
2. В **соединение с сервером** диалогового окна введите *(localdb) \v11.0* как **имя сервера**, оставьте **проверки подлинности** значение **Проверка подлинности Windows**, а затем нажмите кнопку **Connect**.

    ![Среда SSMS соединиться с сервером](preparing-databases/_static/image10.png)
3. В **обозревателя объектов** окна, разверните **баз данных**, щелкните правой кнопкой мыши **aspnet ContosoUniversity**, нажмите кнопку **задачи**и нажмите кнопку **Создания скриптов**.

    ![Среда SSMS создания скриптов](preparing-databases/_static/image11.png)
4. В **формирования и публикации скриптов** диалоговое окно, нажмите кнопку **задание параметров скрипта**.

    Можно пропустить **Выбор объектов** шаг, так как значение по умолчанию — **всей базы данных и все объекты базы данных скрипта** и выберите.
5. Нажмите кнопку **Дополнительно**.

    ![Параметры скриптов SSMS](preparing-databases/_static/image12.png)
6. В **Дополнительные параметры создания скриптов** диалоговое окно, прокрутите список до **типы данных для сценария**и нажмите кнопку **только данные** вариант в раскрывающемся списке.
7. Изменение **скрипт USE DATABASE** для **False**. Использование инструкций не являются допустимыми для базы данных SQL Azure и не требуется для развертывания в SQL Server Express в тестовой среде.

    ![Среда SSMS скрипт только данные, инструкция USE](preparing-databases/_static/image13.png)
8. Нажмите кнопку **ОК**.
9. В **формирования и публикации скриптов** диалоговом **имя файла** поле указывает, где будет создан скрипт. Измените путь к папке решения (папку, файл ContosoUniversity.sln) и имя файла для *aspnet данных dev.sql*.
10. Нажмите кнопку **Далее** для перехода к **Сводка** , а затем щелкните **Далее** еще раз, чтобы создать скрипт.

    ![Создать скрипт среды SSMS](preparing-databases/_static/image14.png)
11. Нажмите кнопку **Готово**.

### <a name="create-the-production-database-script"></a>Создайте скрипт рабочей базы данных

Так как проект еще не запущена с рабочей базой данных, она не еще привязывается к экземпляру LocalDB. Поэтому необходимо сначала присоединить базу данных.

1. В SSMS **обозревателя объектов**, щелкните правой кнопкой мыши **баз данных** и нажмите кнопку **присоединение**.

    ![Присоединение SSMS](preparing-databases/_static/image15.png)
- В **присоединение баз данных** диалоговое окно, нажмите кнопку **добавить** и перейдите к *aspnet ContosoUniversity-Prod.mdf* файла в *приложения\_ Данные* папки.

    ![Добавить SSMS MDF-файл для присоединения](preparing-databases/_static/image16.png)
- Нажмите кнопку **ОК**.
- Выполните ту же процедуру, которую вы использовали для создания скрипта для файла рабочей. Имя файла скрипта *aspnet данных prod.sql*.

## <a name="summary"></a>Сводка

Обе базы данных теперь готовы к развертыванию и у вас есть два сценария развертывания данных в папке решения.

![Скрипты развертывания данных](preparing-databases/_static/image17.png)

В этом руководстве настроить параметры проекта, влияющие на развертывание и настройка автоматического *Web.config* файл преобразования для параметров, которые должны быть разными в развернутом приложении.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения о NuGet см. в разделе [Управление библиотеками проектов с помощью NuGet](https://msdn.microsoft.com/magazine/hh547106.aspx) и [документации по NuGet](http://docs.nuget.org/docs/start-here/overview). Если вы не хотите использовать NuGet, необходимо узнать, как анализировать пакет NuGet, чтобы определить, что делает при установке. (Например, она может настроить *Web.config* преобразования, настраивать сценарии PowerShell для выполнения во время сборки и т. д.) Дополнительные сведения о работе NuGet см. в разделе [создания и публикации пакета](http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-package) и [файла конфигурации и преобразования кода источника](http://docs.nuget.org/docs/creating-packages/configuration-file-and-source-code-transformations).

>[!div class="step-by-step"]
[Назад](introduction.md)
[Вперед](web-config-transformations.md)
