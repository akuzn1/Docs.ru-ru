---
title: "Размещение ASP.NET Core в Windows со службами IIS"
author: guardrex
description: "Сведения о размещении приложений ASP.NET Core в службах Windows Server Internet Information Services (IIS)."
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/iis/index
ms.openlocfilehash: fa9e60c52f143b20dbf179679fc4932e838a9137
ms.sourcegitcommit: 493a215355576cfa481773365de021bcf04bb9c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Размещение ASP.NET Core в Windows со службами IIS

Авторы: [Люк Лэтэм](https://github.com/guardrex) (Luke Latham) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Поддерживаются следующие операционные системы:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии.&#8224;

&#8224; В принципе, конфигурация IIS, описанная в этом документе, также подходит для размещения приложений ASP.NET Core в службах IIS на сервере Nano Server. Инструкции для Nano Server см. в руководстве [ASP.NET Core с IIS в Nano Server](xref:tutorials/nano-server).

[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался [WebListener](xref:fundamentals/servers/weblistener)) не работает в конфигурации обратного прокси-сервера со службами IIS. Используйте [сервер Kestrel](xref:fundamentals/servers/kestrel).

## <a name="application-configuration"></a>Настройка приложения

### <a name="enable-the-iisintegration-components"></a>Включение компонентов IISIntegration

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Обычно *Program.cs* вызывает [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), чтобы начать настройку узла. `CreateDefaultBuilder` настраивает [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и активирует интеграцию IIS, задавая базовый путь и порт для [модуля ASP.NET Core](xref:fundamentals/servers/aspnet-core-module):

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Модуль ASP.NET Core создает динамический порт для назначения серверному процессу. Метод `UseIISIntegration` принимает этот динамический порт и настраивает Kestrel для прослушивания адресу `http://locahost:{dynamicPort}/`. Это переопределяет другие конфигурации URL-адресов, такие как вызовы `UseUrls` или [API прослушивания Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration). Таким образом, вызовы `UseUrls` или API `Listen` Kestrel при работе с этим модулем не требуются. При вызове `UseUrls` или `Listen` Kestrel прослушивает порт, указанный при выполнении приложения без IIS.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Включите в зависимости приложения зависимость от пакета [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/). Используйте ПО промежуточного слоя для интеграции IIS, добавив метод расширения [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) в [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

Оба метода, [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) и [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration), — обязательные. Код, вызывающий `UseIISIntegration`, не влияет на переносимость кода. Если приложение запускается не в IIS (например, запускается непосредственно в Kestrel), `UseIISIntegration` не работает.

Модуль ASP.NET Core создает динамический порт для назначения серверному процессу. Метод `UseIISIntegration` принимает этот динамический порт и настраивает Kestrel для прослушивания адресу `http://locahost:{dynamicPort}/`. Это переопределяет другие конфигурации URL-адресов, такие как вызовы `UseUrls`. Таким образом, при использовании этого модуля вызов `UseUrls` не требуется. При вызове `UseUrls` Kestrel прослушивает порт, указанный при выполнении приложения без IIS.

При вызове `UseUrls` в приложении ASP.NET Core 1.0 следует выполнять вызов **до** вызова `UseIISIntegration`, чтобы исключить перезапись порта, настроенного в модуле. В ASP.NET Core 1.1 соблюдать этот порядок вызовов не требуется, так как параметр модуля переопределяет `UseUrls`.

---

Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/hosting).

### <a name="iis-options"></a>Параметры служб IIS

Чтобы настроить параметры IIS, включите конфигурацию службы для [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) в [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices). В примере ниже пересылка сертификатов клиентов в приложение с целью заполнения `HttpContext.Connection.ClientCertificate` отключена.

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Параметр                         | Значение по умолчанию | Параметр |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Если значение — `true`, ПО промежуточного слоя для интеграции IIS задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth). Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`. Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows. Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Задает отображаемое имя для пользователей на страницах входа. |
| `ForwardClientCertificate`     | `true`  | Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`. |

### <a name="webconfig-file"></a>Файл web.config

В файле *web.config* содержится конфигурация [модуля ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). За создание, преобразования и публикацию *web.config* отвечает веб-пакет SDK для .NET Core (`Microsoft.NET.Sdk.Web`). Пакет SDK задается в начале файла проекта.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Если в проекте нет файла *web.config*, он создается с соответствующими аргументами *processPath* и *arguments* для настройки [модуля ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) и переносится в [опубликованные выходные данные](xref:host-and-deploy/directory-structure).

Если в проекте есть файл *web.config*, он преобразуется с соответствующими аргументами *processPath* и *arguments* для настройки модуля ASP.NET Core и переносится в опубликованные выходные данные. Преобразование не изменяет параметры конфигурации служб IIS, включенные в файл.

Файл *web.config* может содержать дополнительные параметры конфигурации IIS, управляющие активными модулями IIS. Сведения о модулях IIS, которые могут обрабатывать запросы к приложениям ASP.NET Core, см. в статье [Using IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules) (Использование модулей IIS с ASP.NET Core).

Чтобы пакет SDK не преобразовывал файл *web.config*, добавьте в файл проекта свойство **\<IsTransformWebConfigDisabled>**.

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Если пакет SDK не преобразует файл, аргументы *processPath* и *arguments* нужно задать вручную. Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>Расположение файла web.config

Приложения ASP.NET Core размещаются на обратном прокси-сервере между службами IIS и сервером Kestrel. Для создания обратного прокси-сервера файл *web.config* должен находиться в корневой папке с содержимым развернутого приложения (как правило, это основной путь приложения). Это расположение соответствует физическому пути веб-сайта, указанному в службах IIS. Файл *web.config* требуется в корне приложения, чтобы разрешить публикацию нескольких приложений с помощью веб-развертывания.

По физическому пути приложения находятся файлы с конфиденциальной информацией: *\<имя_сборки>.runtimeconfig.json*, *\<имя_сборки>.xml* (комментарии к XML-документации) и *\<имя_сборки>.deps.json*. Когда файл *web.config* присутствует и сайт запускается нормально, службы IIS не обрабатывают запросы к этим файлам. Если файл *web.config* отсутствует, неправильно именован или не может настроить нормальный запуск сайта, службы IIS могут свободно передавать содержимое этих конфиденциальных файлов.

**Файл *web.config* должен постоянно находиться в развертывании, у этого файла должно быть правильное имя, и файл должен быть в состоянии настроить нормальный запуск сайта. Никогда не удаляйте файл *web.config* из развертывания в рабочей среде.**

## <a name="iis-configuration"></a>Конфигурация IIS

**Операционные системы семейства Windows Server**

Включите роль сервера **Веб-сервер (IIS)** и настройте службы роли.

1. В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку. На этапе **Роли сервера** установите флажок **Веб-сервер (IIS)**.

   ![Роль "Веб-сервер (IIS)" выбрана на странице "Выбор ролей сервера".](index/_static/server-roles-ws2016.png)

1. После этапа выбора **компонентов** запускается этап выбора **служб роли** для веб-сервера (IIS). Выберите нужные службы роли IIS или оставьте службы по умолчанию.

   ![Службы роли по умолчанию, выбранные на странице "Выбор служб ролей".](index/_static/role-services-ws2016.png)

   **Проверка подлинности Windows (необязательный компонент)**  
   Чтобы включить проверку подлинности Windows, разверните такие узлы: **Веб-сервер** > **Безопасность**. Выберите компонент **Проверка подлинности Windows**. Дополнительные сведения см. в статьях [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) (Проверка подлинности Windows <windowsAuthentication>) и [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).

   **Протокол WebSocket (необязательный компонент)**  
   Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1. Чтобы включить протокол WebSocket, разверните такие узлы: **Веб-сервер** > **Разработка приложений**. Выберите компонент **Протокол WebSocket**. Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).

1. Пройдите этап **Подтверждение**, чтобы установить роль и службы веб-сервера. После установки роли **Веб-сервер (IIS)** перезагружать сервер или службы IIS не требуется.

**Операционные системы Windows для настольных компьютеров**

Включите компоненты **Консоль управления IIS** и **Службы Интернета**.

1. Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).

1. Разверните узел **Службы IIS**. Разверните узел **Средства управления веб-сайтом**.

1. Установите флажок **Консоль управления IIS**.

1. Установите флажок **Службы Интернета**.

1. В группе **Службы Интернета** оставьте компоненты по умолчанию или настройте компоненты IIS.

   **Проверка подлинности Windows (необязательный компонент)**  
   Чтобы включить проверку подлинности Windows, разверните такие узлы: **Службы Интернета** > **Безопасность**. Выберите компонент **Проверка подлинности Windows**. Дополнительные сведения см. в статьях [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) (Проверка подлинности Windows <windowsAuthentication>) и [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).

   **Протокол WebSocket (необязательный компонент)**  
   Протокол WebSocket поддерживается в ASP.NET Core начиная с версии 1.1. Чтобы включить протокол WebSocket, разверните такие узлы: **Службы Интернета** > **Компоненты разработки приложений**. Выберите компонент **Протокол WebSocket**. Дополнительные сведения см. в разделе [Протокол WebSocket](xref:fundamentals/websockets).

1. Если во время установки IIS потребуется перезагрузка, перезагрузите систему.

![Группы "Консоль управления IIS" и "Службы Интернета" выбраны в окне "Компоненты Windows".](index/_static/windows-features-win10.png)

---

## <a name="install-the-net-core-windows-server-hosting-bundle"></a>Установка пакета размещения .NET Core для Windows Server

1. Установите [пакет размещения .NET Core для Windows Server](https://aka.ms/dotnetcore-2-windowshosting) в размещающей системе. В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Модуль создает обратный прокси-сервер между службами IIS и сервером Kestrel. Если система не подключена к Интернету, перед установкой пакета размещения .NET Core для Windows Server получите и установите [Распространяемый компонент Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=53840).

   **Важно!** Если пакет размещения был установлен до установки служб IIS, пакет необходимо восстановить. После установки служб IIS запустите установщик пакета размещения еще раз.
   
   Чтобы запретить установщику установку пакетов x86 в операционной системе x64, запускать установщик следует из командной строки с правами администратора с параметром `OPT_NO_X86=1`.

1. Перезагрузите систему или в командой строке выполните команду **net stop was /y**, а затем — команду **net start w3svc**. Перезапуск служб IIS позволит обнаружить изменения в системном пути, которые внес установщик.

> [!NOTE]
> Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Установка веб-развертывания при публикации с помощью Visual Studio

При развертывании приложений на серверах с помощью [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) установите на сервере последнюю версию веб-развертывания. Чтобы установить веб-развертывание, можно использовать [установщик веб-платформы (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) или получить установщик непосредственно в [Центре загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=43717). Предпочтительный метод — использовать WebPI. WebPI обеспечивает автономную установку и настройку поставщиков размещения.

## <a name="create-the-iis-site"></a>Создание сайта IIS

1. В размещающей системе создайте папку, в которой будут храниться файлы и папки опубликованного приложения. Макет развертывания приложения описан в статье [Directory structure of published ASP.NET Core apps](xref:host-and-deploy/directory-structure) (Структура каталогов опубликованных приложений ASP.NET Core).

1. В созданной только что папке создайте папку *logs*, в которой будут храниться журналы StdOut модуля ASP.NET Core (если включено ведение таких журналов). Если приложение развертывается с папкой *logs* в полезных данных, пропустите этот шаг. Сведения о том, как в MSBuild настроить автоматическое создание папки *logs* при создании проекта локально, см. в статье [Directory structure of published ASP.NET Core apps](xref:host-and-deploy/directory-structure) (Структура каталогов опубликованных приложений ASP.NET Core).

   **Важно!** Журнал StdOut следует использовать только для устранения ошибок, возникающих при запуске приложения. Никогда не используйте журнал StdOut как журнал повседневных операций приложения. Ни размер файла журнала, ни количество создаваемых файлов журналов ничем не ограничены. Дополнительные сведения о журнале StdOut см. в разделе о [создании и перенаправлении журналов](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection). Сведения о ведении журнала приложения ASP.NET Core см. в статье [Общие сведения о ведении журналов в ASP.NET Core](xref:fundamentals/logging/index).

1. В окне **Диспетчер служб IIS** в области **Подключения** разверните узел сервера. Щелкните правой кнопкой мыши папку **Сайты**. В контекстном меню выберите пункт **Добавить веб-сайт**.

1. Укажите имя в поле **Имя сайта** и задайте значение в поле **Физический путь** для созданной папки развертывания приложения. Укажите конфигурацию **привязки** и нажмите кнопку **ОК**, чтобы создать веб-сайт.

   ![В окне "Добавить веб-сайт" укажите имя сайта, физический путь и имя узла.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Это может создать уязвимость и поставить ваше приложение под угрозу. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо этого используйте имена узлов в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Разверните узел сервера и выберите **Пулы приложений**.

1. Щелкните правой кнопкой мыши пул приложений сайта и в контекстном меню выберите пункт **Основные параметры**.

1. В окне **Изменение пула приложений** задайте для параметра **Версия среды CLR .NET** значение **Без управляемого кода**.

   ![Выбор значения "Без управляемого кода" для параметра "Версия среды CLR .NET".](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core выполняется в отдельном процессе и управляет средой выполнения. Для ASP.NET Core не требуется загрузка классической среды CLR. Задавать для параметра **Версия среды CLR .NET** значение **Без управляемого кода** необязательно.

1. Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.

   При изменении удостоверения по умолчанию для пула приложений (**Модель процесса** > **Удостоверение**) с **ApplicationPoolIdentity** на другое, убедитесь, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам. Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.

**Настройка проверки подлинности Windows (необязательный этап)**  
См. статью [Configure Windows authentication in an ASP.NET Core app](xref:security/authentication/windowsauth) (Настройка проверки подлинности Windows в приложении ASP.NET Core).

## <a name="deploy-the-app"></a>Развертывание приложения

Разверните приложение в папке, созданной в размещающей системе. Рекомендуемый механизм развертывания — [веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy).

### <a name="web-deploy-with-visual-studio"></a>Веб-развертывание с помощью Visual Studio

Сведения о создании профиля публикации для веб-развертывания см. в статье [Профили публикации Visual Studio для развертывания приложений ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Если поставщик услуг размещения предоставляет профиль публикации или позволяет его создать, скачайте этот профиль и импортируйте его с помощью диалогового окна **Публикация** в Visual Studio.

![Диалоговое окно "Публикация"](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Веб-развертывание вне Visual Studio

[Веб-развертывание](/iis/publish/using-web-deploy/introduction-to-web-deploy) можно также использовать вне Visual Studio с помощью командной строки. Дополнительные сведения см. в статье [Средство веб-развертывания](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Альтернативы веб-развертыванию

Переместить приложение в размещающую систему можно несколькими способами: с помощью копирования вручную, Xcopy, Robocopy или PowerShell.

## <a name="browse-the-website"></a>Обзор веб-сайта

![Начальная страница IIS, загруженная в браузере Microsoft Edge.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Заблокированные файлы развертывания

Во время выполнения приложения файлы в папке развертывания блокируются. Заблокированные файлы невозможно перезаписать во время развертывания. Чтобы снять блокировку с файлов в развертывании, остановите пул приложений с помощью **одного** из следующих методов:

* Запустите веб-развертывание и добавьте ссылку на `Microsoft.NET.Sdk.Web` в файл проекта. Файл *app_offline.htm* помещается в корень каталога веб-приложения. Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания. Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#appofflinehtm).
* Вручную остановите пул приложений в диспетчере служб IIS на сервере.
* Используйте PowerShell для остановки и перезапуска пула приложений (требуется PowerShell 5 или более поздняя версия).

  ```PowerShell
  $webAppPoolName = 'APP_POOL_NAME'

  # Stop the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
      Stop-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Stopped') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Stopped
  }

  # Provide script commands here to deploy the app

  # Restart the AppPool
  if((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
      Start-WebAppPool -Name $webAppPoolName
      while((Get-WebAppPoolState $webAppPoolName).Value -ne 'Started') {
          Start-Sleep -s 1
      }
      Write-Host `-AppPool Started
  }
  ```

## <a name="data-protection"></a>Защита данных

[Стек защиты данных ASP.NET Core](xref:security/data-protection/index) используют несколько [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая ПО, которое применяется для аутентификации. Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, защиту данных следует настроить для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Это можно сделать с помощью скрипта развертывания или в пользовательском коде. Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.

Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:

* Все токены аутентификации, использующие файлы cookie, становятся недействительными. 
* При выполнении следующего запроса пользователю требуется выполнить вход снова. 
* Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы. Это могут быть [токены CSRF](xref:security/anti-request-forgery#how-does-aspnet-core-mvc-address-csrf) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).

Чтобы настроить защиту данных в службах IIS для хранения набора ключей, воспользуйтесь **одним** из следующих методов:

* **Создание раздела реестра для защиты данных**.

  Ключи защиты данных, используемые приложениями ASP.NET Core, хранятся во внешнем для приложений реестре. Чтобы хранить эти ключи для определенного приложения, нужно создать разделы реестра для пула приложений.

  При автономной установке служб IIS, не поддерживающей веб-ферму, можно выполнить [скрипт PowerShell Provision-AutoGenKeys.ps1 для защиты данных](https://github.com/aspnet/DataProtection/blob/dev/Provision-AutoGenKeys.ps1) применительно к каждому пулу приложений, в который входит приложение ASP.NET Core. Этот скрипт создает раздел в реестре HKLM, который будет доступен только для учетной записи рабочего процесса пула приложений, к которому относится соответствующее приложение. Неактивные ключи шифруются с помощью API защиты данных с ключом компьютера.

  В случаях, когда используется веб-ферма, в приложении можно настроить UNC-путь, по которому это приложение будет хранить набор ключей для защиты данных. По умолчанию ключи защиты данных не шифруются. Разрешения на доступ к файлам в сетевой папке должны быть предоставлены только учетной записи Windows, с помощью которой выполняется приложение. Для защиты неактивных ключей можно использовать сертификат X509. Рассмотрите возможность реализации механизма, позволяющего пользователям отправлять сертификаты: поместить сертификаты в хранилище доверенных сертификатов пользователя и обеспечивать их доступность на всех компьютерах, где выполняется приложение. Подробные сведения см. в разделе [Настройка защиты данных](xref:security/data-protection/configuration/overview).

* **Настройка пула приложений IIS для загрузки профиля пользователя**.

  Этот параметр находится на странице **Дополнительные параметры** пула приложений в разделе **Модель процесса**. Задайте для параметра "Загрузить профиль пользователя" значение `True`. В результате ключи будут храниться в каталоге профиля пользователя, а их безопасность будет обеспечиваться за счет применения API защиты данных и ключа на уровне учетной записи пользователя, которую использует пул приложений.

* **Использование файловой системы в качестве хранилища набора ключей**.

  Измените код приложения так, чтобы [в качестве хранилища набора ключей использовалась файловая система](xref:security/data-protection/configuration/overview). Для защиты набора ключей используйте доверенный сертификат X509. Если сертификат — самозаверяющий, поместите его в доверенное корневое хранилище.

  При использовании служб IIS в веб-ферме:

  * используйте общую папку, которая доступна всем компьютерам;
  * разверните сертификат X509 на каждом компьютере; настройте [защиту данных в коде](xref:security/data-protection/configuration/overview).

* **Настройка политики защиты данных на уровне компьютера**.

  Система защиты данных обеспечивает ограниченную поддержку задания [политики по умолчанию на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy) для всех приложений, использующих интерфейсы API защиты данных. Дополнительные сведения см. в [документации по защите данных](xref:security/data-protection/index).

## <a name="sub-application-configuration"></a>Конфигурация дочерних приложений

Дочерние приложения, добавленные в корневое приложение, не должны включать в себя модуль ASP.NET Core в качестве обработчика. Если модуль добавляется в качестве обработчика в файл *web.config* дочернего приложения, при попытке работы с этим приложением выводится ошибка *500.19* (внутренняя ошибка сервера) с указанием некорректного файла конфигурации.

Вот пример опубликованного файла *web.config* для дочернего приложения ASP.NET Core:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

При размещении дочернего приложения не на основе ASP.NET Core в приложении ASP.NET Core, нужно явным образом удалить унаследованный обработчик из файла *web.config* дочернего приложения.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\<assembly_name>.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Дополнительные сведения о настройке модуля ASP.NET Core см. в статье [Введение в модуль ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) и в [справочнике по настройке модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="configuration-of-iis-with-webconfig"></a>Настройка служб IIS с помощью файла web.config

Раздел **\<system.webServer>** файла *web.config* действует для тех компонентов IIS, которые относятся к конфигурации прокси-сервера. Если в службах IIS на уровне сервера настроено динамическое сжатие, элемент **\<urlCompression>** в файле *web.config* приложения может отключить это сжатие.

Дополнительные сведения см. в [справочнике по настройке \<system.webServer>](/iis/configuration/system.webServer/), [справочнике по настройке модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) и статье [Using IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules) (Использование модулей IIS с ASP.NET Core). Сведения о настройке переменных среды для отдельных приложений, выполняющихся в изолированных пулах приложений (такая возможность поддерживается в службах IIS начиная с версии 10.0), см. в справочной документации по службам IIS, в частности в разделе *AppCmd.exe* статьи [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) (Переменные среды <environmentVariables>).

## <a name="configuration-sections-of-webconfig"></a>Разделы конфигурации файла web.config

Разделы конфигурации приложений ASP.NET 4.x в файле *web.config* не используются для конфигурации приложений ASP.NET Core.

* **\<system.web>**
* **\<appSettings>**
* **\<connectionStrings >**
* **\<location>**

Для настройки приложений ASP.NET Core используются другие поставщики конфигураций. Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Пулы приложений

Если на одном сервере размещается несколько веб-сайтов, изолируйте приложения друг от друга, запуская каждое из них в отдельном пуле приложений. В диалоговом окне **Добавить веб-сайт** служб IIS такая конфигурация задана по умолчанию. Если указано **имя сайта**, оно автоматически переносится в текстовое поле **Пул приложений**. При добавлении веб-сайта создается пул приложений с именем сайта.

## <a name="application-pool-identity"></a>Удостоверение пула приложений

Учетная запись удостоверения пула приложений позволяет запускать приложение от имени уникальной учетной записи, не создавая домены или локальные учетные записи и не управляя ими. В службах IIS начиная с версии 8.0 рабочий процесс администратора IIS (WAS) создает виртуальную учетную запись с именем нового пула приложений и по умолчанию выполняет рабочие процессы пула приложений с помощью этой учетной записи. В консоли управления IIS в окне **Дополнительные параметры** для пула приложений должно быть выбрано **Удостоверение** **ApplicationPoolIdentity**.

![Диалоговое окно дополнительных параметров для пула приложений](index/_static/apppool-identity.png)

Процесс управления IIS создает в системе безопасности Windows безопасный идентификатор с именем пула приложений. С помощью этого идентификатора можно защищать ресурсы, но он не является реальной учетной записью и не отображается в консоли управления пользователями Windows.

Если рабочему процессу IIS требуется предоставить доступ к приложению с повышенными правами, измените список управления доступом (ACL) для каталога, содержащего приложение.

1. Откройте проводник и перейдите к каталогу.

1. Щелкните каталог правой кнопкой мыши и выберите пункт **Свойства**.

1. На вкладке **Безопасность** нажмите кнопку **Изменить**, а затем — кнопку **Добавить**.

1. Нажмите кнопку **Размещение** и выберите систему.

1. В поле **Введите имена выбираемых объектов** введите **IIS AppPool\\<имя_пула_приложений>**. Нажмите кнопку **Проверить имена**. В случае с *DefaultAppPool* проверьте имена с помощью **IIS AppPool\DefaultAppPool**. После нажатия кнопки **Проверить имена** в области имен объектов отобразится значение **DefaultAppPool**. Вручную ввести имя пула приложений в области имен объектов нельзя. При поиске имени объекта используйте формат **IIS AppPool\\<имя_пула_приложений>**.

   ![Диалоговое окно выбора пользователей и групп для папки приложения. До нажатия кнопки "Проверить имена" в области имен объектов к строке IIS AppPool\" добавилось имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-1.png)

1. Нажмите кнопку **ОК**.

   ![Диалоговое окно выбора пользователей и групп для папки приложения. После нажатия кнопки "Проверить имена" в области имен объектов отображается имя пула приложений, DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. Разрешения на чтение и выполнение предоставляются по умолчанию. При необходимости предоставьте дополнительные разрешения.

Кроме того, доступ можно предоставить через командную строку, используя средство **ICACLS**. В случае с *DefaultAppPool* выполняется такая команда:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Дополнительные сведения об ICACLS см. [здесь](/windows-server/administration/windows-commands/icacls).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Устранение неполадок ASP.NET Core в службах IIS](xref:host-and-deploy/iis/troubleshoot)
* [Справочник по общим ошибкам для службы приложений Azure и служб IIS с ASP.NET Core](xref:host-and-deploy/azure-iis-errors-reference)
* [Введение в модуль ASP.NET Core](xref:fundamentals/servers/aspnet-core-module)
* [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module)
* [Использование модулей IIS с ASP.NET Core](xref:host-and-deploy/iis/modules)
* [Введение в ASP.NET Core](../index.md)
* [Официальный веб-сайт Microsoft IIS](https://www.iis.net/)
* [Библиотека Microsoft TechNet: Windows Server](/windows-server/windows-server-versions)
