---
title: "Razor Pages с Entity Framework Core: учебник 1 из 8"
author: rick-anderson
description: "Сведения о том, как создать приложение Razor Pages с помощью Entity Framework Core"
manager: wpickett
ms.author: riande
ms.date: 11/15/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: data/ef-rp/intro
ms.openlocfilehash: 091f34da347d52ba8e3e87779ddc4aeb790c2800
ms.sourcegitcommit: 18d1dc86770f2e272d93c7e1cddfc095c5995d9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2018
---
# <a name="getting-started-with-razor-pages-and-entity-framework-core-using-visual-studio-1-of-8"></a>Начало работы с Razor Pages и Entity Framework Core в Visual Studio (1 из 8)

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

На примере учебного веб-приложения "Университет Contoso" демонстрируется процесс создания веб-приложений ASP.NET Core 2.0 MVC с помощью Entity Framework (EF) Core 2.0 и Visual Studio 2017.

В этом примере приложения реализуется веб-сайт вымышленного университета Contoso. На нем предусмотрены различные функции, в том числе прием учащихся, создание курсов и назначение преподавателей. Это первое руководство из серии, в котором описывается создание примера приложения для университета Contoso.

[Скачайте или ознакомьтесь с готовым приложением.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Указания по скачиванию](xref:tutorials/index#how-to-download-a-sample).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE[install 2.0](../../includes/install2.0.md)]

Знакомство с [Razor Pages](xref:mvc/razor-pages/index). Новые программисты должны изучить статью [Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start), прежде чем приступать к этой серии.

## <a name="troubleshooting"></a>Устранение неполадок

Если вы столкнулись с проблемами, для их решения можно попробовать сравнить свой код с кодом [готового этапа](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) или [готового проекта](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu-final). Список распространенных ошибок и способы их устранения см. в [разделе "Устранение неполадок" последнего руководства серии](xref:data/ef-mvc/advanced#common-errors). Если вам не удалось найти нужную информацию, вы можете задать вопрос на сайте [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) в разделах, посвященных [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) или [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Эта серия включает в себя руководства, содержание каждого из которых базируется на предыдущих руководствах. После успешного завершения каждого руководства рекомендуется сохранять копию проекта. При возникновении проблем вы сможете вернуться к предыдущему руководству, а не к самому началу. Кроме того, вы можете скачать [завершенный этап](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots) и начать с него.

## <a name="the-contoso-university-web-app"></a>Веб-приложение университета Contoso

Приложение, создаваемое в этих руководствах, является простым веб-сайтом университета.

Пользователи приложения могут просматривать и обновлять сведения об учащихся, курсах и преподавателях. Здесь приведено несколько экранов, создаваемых в руководстве.

![Страница указателя учащихся](intro/_static/students-index.png)

![Страница редактирования учащихся](intro/_static/student-edit.png)

Стиль пользовательского интерфейса для этого сайта близок к обеспечиваемому встроенными шаблонами. Это руководство посвящено EF Core с Razor Pages, а не пользовательскому интерфейсу.

## <a name="create-a-razor-pages-web-app"></a>Создание веб-приложения Razor Pages

* В меню **Файл** Visual Studio откройте меню **Создать** > **Проект**.
* Создайте новое веб-приложение ASP.NET Core. Назовите проект **ContosoUniversity**. Очень важно, чтобы проект имел имя *ContosoUniversity*, чтобы совпадали пространства имен при копировании и вставке кода.
 ![Новое веб-приложение ASP.NET Core](intro/_static/np.png)
* Выберите в раскрывающемся списке **ASP.NET Core 2.0**, а затем **Веб-приложение**.
 ![Веб-приложение (Razor Pages)](../../mvc/razor-pages/index/_static/np2.png)

Нажмите клавишу **F5**, чтобы запустить приложение в режиме отладки, или **CTRL-F5**, чтобы запустить его без подключения отладчика.

## <a name="set-up-the-site-style"></a>Настройка стиля сайта

Выполните небольшую настройку меню, макета и домашней страницы сайта.

Откройте файл *Pages/_Layout.cshtml* и внесите следующие изменения:

* Замените все вхождения "ContosoUniversity" на "Contoso University". Таких элементов будет три.

* Добавьте пункты меню **Students** (Учащиеся), **Courses** (Курсы), **Instructors** (Преподаватели) и **Departments** (Кафедры). Удалите пункт меню **Contact** (Контакты).

Изменения выделены. (Вся разметка *не* отображается.)

[!code-html[](intro/samples/cu/Pages/_Layout.cshtml?highlight=6,29,35-38,47&range=1-50)]

Замените содержимое файла *Pages/Index.cshtml* следующим кодом, который заменяет текст о ASP.NET и MVC описанием этого приложения:

[!code-html[](intro/samples/cu/Pages/Index.cshtml)]

Нажмите клавиши CTRL+F5, чтобы запустить проект. Домашняя страница отображается с вкладками, созданными в следующих руководствах:

![Домашняя страница университета Contoso](intro/_static/home-page.png)

## <a name="create-the-data-model"></a>Создание модели данных

Создайте классы сущностей для приложения университета Contoso. Начните со следующих трех сущностей:

![Схема модели данных "курс-регистрация-учащийся"](intro/_static/data-model-diagram.png)

Между сущностями `Student` и `Enrollment` действует связь один ко многим. Между сущностями `Course` и `Enrollment` действует связь один ко многим. Студент может записаться на любое число курсов. На курс может быть зачислено любое количество учащихся.

В следующих разделах создается класс для каждой из этих сущностей.

### <a name="the-student-entity"></a>Сущность Student

![Схема сущности Student](intro/_static/student-entity.png)

Создайте папку *Models*. В папке *Models* создайте файл класса с именем *Student.cs*, содержащий следующий код:

[!code-csharp[Main](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

Свойство `ID` используется в качестве столбца первичного ключа в таблице базы данных, соответствующей этому классу. По умолчанию платформа EF Core интерпретирует в качестве первичного ключа свойство `ID` или `classnameID`.

Свойство `Enrollments` является свойством навигации. Свойства навигации ссылаются на другие сущности, связанные с этой сущностью. В этом случае свойство `Enrollments` сущности `Student entity` содержит все сущности `Enrollment`, которые связаны с этой сущностью `Student`. Например, если строка "Student" (Учащийся) в базе данных имеет две связанные строки "Enrollment" (зачисление), свойство навигации `Enrollments` содержит две этих сущности `Enrollment`. Связанная строка `Enrollment` содержит значение первичного ключа для этого учащегося в столбце `StudentID`. Предположим, например, что учащийся с идентификатором 1 имеет две строки в таблице `Enrollment`. Таблица `Enrollment` содержит две строки с `StudentID` = 1. `StudentID` является внешним ключом в таблице `Enrollment`, указывающим учащегося в таблице `Student`.

Если свойство навигации может содержать несколько сущностей, оно должно иметь тип списка, такой как `ICollection<T>`. Можно указать `ICollection<T>` либо тип, такой как `List<T>` или `HashSet<T>`. Если используется `ICollection<T>`, платформа EF Core по умолчанию создает коллекцию `HashSet<T>`. Свойства навигации, содержащие несколько сущностей, поступают по связям многие ко многим и один ко многим.

### <a name="the-enrollment-entity"></a>Сущность Enrollment

![Схема сущности Enrollment](intro/_static/enrollment-entity.png)

В папке *Models* создайте файл *Enrollment.cs*, содержащий следующий код:

[!code-csharp[Main](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

Свойство `EnrollmentID` является первичным ключом. Эта сущность использует шаблон `classnameID` вместо `ID`, как сущность `Student`. Обычно разработчики выбирают один шаблон и используют его в рамках всей модели данных. В одном из следующих руководств показано, за счет чего использование идентификатора без имени класса позволяет упростить реализацию наследования в модели данных.

Свойство `Grade` имеет тип `enum`. Знак вопроса после объявления типа `Grade` указывает, что свойство `Grade` допускает значение null. Оценка со значением null отличается от нулевой оценки тем, что при таком значении оценка еще не известна или не назначена.

Свойство `StudentID` представляет собой внешний ключ. Ему соответствует свойство навигации `Student`. Сущность `Enrollment` связана с одной сущностью `Student`, поэтому свойство содержит отдельную сущность `Student`. Сущность `Student` отличается от свойства навигации `Student.Enrollments`, которое содержит несколько сущностей `Enrollment`.

Свойство `CourseID` представляет собой внешний ключ. Ему соответствует свойство навигации `Course`. Сущность `Enrollment` связана с одной сущностью `Course`.

EF Core воспринимает свойство как внешний ключ, если он имеет имя `<navigation property name><primary key property name>`. Например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`. Свойства внешнего ключа также могут называться `<primary key property name>`. Например, `CourseID`, так как сущность `Course` имеет первичный ключ `CourseID`.

### <a name="the-course-entity"></a>Сущность Course

![Схема сущности Course](intro/_static/course-entity.png)

В папке *Models* создайте файл *Course.cs*, содержащий следующий код:

[!code-csharp[Main](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

Свойство `Enrollments` является свойством навигации. Сущность `Course` может быть связана с любым числом сущностей `Enrollment`.

Атрибут `DatabaseGenerated` позволяет приложению указать первичный ключ, а не использовать созданный базой данных.

## <a name="create-the-schoolcontext-db-context"></a>Создание контекста базы данных SchoolContext

Контекст базы данных — это класс main, который координирует функциональные возможности EF Core для заданной модели данных. Контекст данных является производным от `Microsoft.EntityFrameworkCore.DbContext`. Контекст данных указывает сущности, которые включаются в модель данных. В этом проекте соответствующий класс называется `SchoolContext`.

В папке проекта создайте папку *Data*.

В папке *Data* создайте файл *SchoolContext.cs*, содержащий следующий код:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Этот код создает свойство `DbSet` для каждого набора сущностей. В терминологии EF Core:

* Набор сущностей обычно соответствует таблице базы данных.
* Сущность соответствует строке в таблице.

`DbSet<Enrollment>` и `DbSet<Course>` можно опустить. Платформа EF Core включает эти операторы неявно, так как сущность `Student` ссылается на сущность `Enrollment`, а `Enrollment` ссылается на сущность `Course`. В рамках данного руководства оставьте `DbSet<Enrollment>` и `DbSet<Course>` в `SchoolContext`.

При создании базы данных платформа EF Core создает таблицы с именами, соответствующими именам свойств в `DbSet`. Имена свойств для коллекций обычно указаны во множественном числе (Students вместо Student). В среде разработчиков нет единого мнения о том, следует ли использовать имена таблиц во множественном числе. В этих учебниках вместо принятого по умолчанию способа таблицам в DbContext присваиваются имена в единственном числе. Чтобы указать имена таблиц в единственном числе, добавьте следующий выделенный код:

[!code-csharp[Main](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-context-with-dependency-injection"></a>Регистрация контекста с помощью внедрения зависимостей

ASP.NET Core поддерживает [внедрение зависимостей](xref:fundamentals/dependency-injection). С помощью внедрения зависимостей службы (например, контекст базы данных EF Core) регистрируются во время запуска приложения. Затем компоненты, которые используют эти службы (например, Razor Pages), обращаются к ним через параметры конструктора. Код конструктора, который получает экземпляр контекста базы данных, приведен далее в этом руководстве.

Чтобы зарегистрировать `SchoolContext` как службу, откройте файл *Startup.cs* и добавьте выделенные строки в метод `ConfigureServices`.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

Имя строки подключения передается в контекст путем вызова метода для объекта `DbContextOptionsBuilder`. При локальной разработке [система конфигурации ASP.NET Core](xref:fundamentals/configuration/index) считывает строку подключения из файла *appsettings.json*.

Добавьте операторы `using` для пространств имен `ContosoUniversity.Data` и `Microsoft.EntityFrameworkCore`. Выполните построение проекта.

[!code-csharp[Main](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Откройте файл *appsettings.json* и добавьте строку подключения, как показано в следующем коде:

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

Предыдущая строка подключения использует `ConnectRetryCount=0` для предотвращения зависания [SQLClient](https://docs.microsoft.com/dotnet/framework/data/adonet/ef/sqlclient-for-the-entity-framework).

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Строка подключения указывает базу данных SQL Server LocalDB. LocalDB — это упрощенная версия ядра СУБД SQL Server Express, предназначенная для разработки приложений и не ориентированная на использование в рабочей среде. LocalDB запускается по запросу в пользовательском режиме, поэтому настройки не слишком сложны. По умолчанию LocalDB создает файлы базы данных *MDF* в каталоге `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Добавление кода для инициализации базы данных с использованием тестовых данных

EF Core создает пустую базу данных. В этом разделе создается метод *Seed*, предназначенный для заполнения тестовыми данными.

В папке *Data* создайте файл класса с именем *DbInitializer.cs* и добавьте следующий код:

[!code-csharp[Main](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Код проверяет наличие учащихся в базе данных. Если учащихся в базе данных нет, она заполняется тестовыми данными. Для повышения производительности тестовые данные загружаются массивами, а не коллекциями `List<T>`.

Метод `EnsureCreated` автоматически создает базу данных для контекста базы данных. Если такая база данных существует, `EnsureCreated` возвращает управление без изменения базы данных.

В файле *Program.cs* измените метод `Main`, чтобы реализовать следующее:

* Получение экземпляра контекста базы данных из контейнера внедрения зависимостей.
* Вызов метода инициализации с передачей ему контекста.
* Высвобождение контекста после завершения работы метода заполнения.

В следующем примере кода показан обновленный файл *Program.cs*.

[!code-csharp[Main](intro/samples/cu/ProgramOriginal.cs?name=snippet)]

При первом запуске приложения создается база данных, которая заполняется тестовыми данными. При обновлении модели данных:
* Удалите базу данных.
* Обновите метод заполнения.
* Запустите приложение, чтобы создать базу данных, заполненную начальными значениями. 

Последующие руководства описывают, как обновить базу данных при изменении модели данных, не прибегая к ее удалению и повторному созданию.

<a name="pmc"></a>
## <a name="add-scaffold-tooling"></a>Добавление средств для формирования шаблонов

В этом разделе с помощью консоли диспетчера пакетов (PMC) добавляется пакет создания веб-кода Visual Studio. Этот пакет необходим для работы ядра формирования шаблонов.

В меню **Сервис** последовательно выберите пункты **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

Введите в консоли диспетчера пакетов (PMC) следующие команды:

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Utils
```

Предыдущая команда добавляет в файл *.csproj пакеты NuGet:

[!code-csharp[Main](intro/samples/cu/ContosoUniversity1_csproj.txt?highlight=7-8)]

<a name="scaffold"></a>
## <a name="scaffold-the-model"></a>Формирование шаблонов для модели

* Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).
* Выполните следующие команды:


 ```console
dotnet restore
dotnet aspnet-codegenerator razorpage -m Student -dc SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
 ```
 
При возникновении следующей ошибки:

```text
Unhandled Exception: System.IO.FileNotFoundException: 
Could not load file or assembly 
'Microsoft.VisualStudio.Web.CodeGeneration.Utils, 
Version=2.0.0.0, Culture=neutral, PublicKeyToken=adb9793829ddae60'.
The system cannot find the file specified.
```

Выполните команду еще раз и оставьте комментарий в нижней части страницы.

Если возникает ошибка.
  ```
No executable found matching command "dotnet-aspnet-codegenerator"
  ```

Откройте окно командной строки в папке проекта (папке, где находятся файлы *Program.cs*, *Startup.cs* и *.csproj* файлов).


Выполните построение проекта. Эта сборка выдает ошибки, например следующего характера:

 `1>Pages\Students\Index.cshtml.cs(26,38,26,45): error CS1061: 'SchoolContext' does not contain a definition for 'Student'`

 Выполните глобальную замену `_context.Student` на `_context.Students` (она заключается в добавлении "s" к `Student`). Обнаруживаются и изменяются 7 экземпляров. Мы постараемся исправить [эту ошибку](https://github.com/aspnet/Scaffolding/issues/633) в следующем выпуске.

[!INCLUDE[model4tbl](../../includes/RP/model4tbl.md)]

 <a name="test"></a>
### <a name="test-the-app"></a>Тестирование приложения

Запустите приложение и выберите ссылку **Students** (Учащиеся). В зависимости от ширины браузера ссылка **Students** (Учащиеся) появляется в верхней части страницы. Если ссылка **Students** (Учащиеся) не отображается, щелкните значок навигации в правом верхнем углу.

![Уменьшенное окно с домашней страницей университета Contoso](intro/_static/home-page-narrow.png)

Проверьте работу ссылок **Create** (Создать), **Edit** (Изменить) и **Details** (Сведения).

## <a name="view-the-db"></a>Просмотр базы данных

При запуске приложения `DbInitializer.Initialize` вызывает `EnsureCreated`. `EnsureCreated` определяет, существует ли база данных, и при необходимости создает ее. Если учащихся в базе данных нет, метод `Initialize` добавляет их.

Откройте **обозреватель объектов SQL Server** (SSOX) из меню **Вид** в Visual Studio.
В SSOX щелкните **(localdb)\MSSQLLocalDB > Базы данных > ContosoUniversity1**.

Разверните узел **Таблицы**.

Щелкните правой кнопкой мыши таблицу **Student** (Учащийся) и выберите пункт **Просмотр данных**, чтобы просмотреть созданные столбцы и строки, вставленные в таблицу.

Файлы базы данных с расширением *MDF* и *LDF* располагаются в папке *C:\Users\\<yourusername>*.

`EnsureCreated` вызывается при запуске приложения, что обеспечивает следующий рабочий процесс:

* Удалите базу данных.
* Измените схему базы данных (например, добавьте поле `EmailAddress`).
* Запустите приложение.

`EnsureCreated` создает базу данных со столбцом `EmailAddress`.

## <a name="conventions"></a>Соглашения

Объем кода, написанного для создания полной базы данных платформой EF Core, сведен к минимуму благодаря использованию соглашений или предположений, задаваемых EF Core.

* В качестве имен таблиц используются имена свойств `DbSet`. Для сущностей, на которые не ссылается свойство `DbSet`, в качестве имен таблиц используются имена классов сущностей.

* В качестве имен столбцов используются имена свойств сущностей.

* Свойства сущностей с именем ID или classnameID распознаются как свойства первичного ключа.

* Свойство интерпретируется как свойство внешнего ключа, если оно имеет имя *<navigation property name><primary key property name>* (например, `StudentID` для свойства навигации `Student`, так как сущность `Student` имеет первичный ключ `ID`). Свойства внешнего ключа могут называться *<primary key property name>* (например, `EnrollmentID`, так как сущность `Enrollment` имеет первичный ключ `EnrollmentID`).

Стандартное поведение можно переопределить. Например, можно явно задать имена таблиц, как было показано ранее в этом руководстве. Можно явно задать имена столбцов. Можно явно задать первичные и внешние ключи.

## <a name="asynchronous-code"></a>Асинхронный код

Асинхронное программирование по умолчанию применяется в ASP.NET Core и EF Core.

Веб-сервер имеет ограниченное число потоков, поэтому при высокой загрузке могут использоваться все доступные потоки. В таких случаях сервер не может обрабатывать новые запросы до тех пор, пока не будут высвобождены потоки. В синхронном коде многие потоки могут быть заняты, не выполняя при этом какие-либо операции и ожидая завершения ввода-вывода. В асинхронном коде в то время, когда процесс ожидает завершения ввода-вывода, его поток высвобождается и может использоваться сервером для обработки других запросов. Таким образом, асинхронный код позволяет более эффективно использовать ресурсы сервера, который может обрабатывать больше трафика без задержек.

Во время выполнения асинхронный код использует немного больше служебных ресурсов. Однако при низком объеме трафика этим можно пренебречь. Тем не менее в случае большого объема трафика это дает существенный выигрыш в производительности.

В следующем коде для асинхронного выполнения используются ключевое слово `async`, возвращаемое значение `Task<T>`, ключевое слово `await` и метод `ToListAsync`.

[!code-csharp[Main](intro/samples/cu/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Ключевое слово `async` указывает компилятору:

  * создавать обратные вызовы для частей тела метода;
  * автоматически создавать возвращаемый объект [Task](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task?view=netframework-4.7). Дополнительные сведения см. в разделе [Тип возвращаемых значений задач](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Неявный тип возвращаемого значения `Task` представляет текущую операцию.

* Ключевое слово `await` предписывает компилятору разделить метод на две части. Первая часть завершается операцией, которая запускается в асинхронном режиме. Вторая часть помещается в метод обратного вызова, который вызывается при завершении операции.

* `ToListAsync` является асинхронной версией метода расширения `ToList`.

При написании асинхронного кода, который использует EF Core, нужно учитывать некоторые моменты:

* Асинхронно выполняются только те операторы, в результате которых в базу данных отправляются запросы или команды. К ним относятся `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` и `SaveChangesAsync`. В их число не входят операторы, которые просто изменяют `IQueryable`, такие как `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Контекст EF Core не является потокобезопасным, поэтому не следует пытаться выполнять несколько операций параллельно. 

* Чтобы воспользоваться преимуществами повышенной производительности асинхронного кода, убедитесь, что пакеты библиотек (например, для разбиения на страницы) используют асинхронную модель, когда вызывают методы EF Core, которые направляют запросы в базу данных.

Дополнительные сведения об асинхронных методах программирования в .NET см. в разделе [Обзор асинхронной модели](https://docs.microsoft.com/dotnet/articles/standard/async).

Следующее руководство посвящено базовым операциям CRUD (создание, чтение, обновление, удаление).

>[!div class="step-by-step"]
[Вперед](xref:data/ef-rp/crud)
