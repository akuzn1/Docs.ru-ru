---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-remote-agent
title: "Настройка веб-сервера для веб-развертывания публикации (удаленный агент) | Документы Microsoft"
author: jrjlee
description: "В этом разделе описываются способы настройки веб-сервер Internet Information Services (IIS), для поддержки веб-публикации и развертывания с помощью веб-развертывания IIS..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 239c7aa8-d09a-4d02-9c0e-6bd52be5f0d5
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-remote-agent
msc.type: authoredcontent
ms.openlocfilehash: 23195949121cd13ca4b1809b8db91a7320d1fdd2
ms.sourcegitcommit: 493a215355576cfa481773365de021bcf04bb9c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
---
<a name="configuring-a-web-server-for-web-deploy-publishing-remote-agent"></a>Настройка веб-сервер для публикации (удаленный агент) веб-развертывания
====================
по [Джейсон Lee](https://github.com/jrjlee)

[Скачать PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> В этом разделе описываются способы настройки веб-сервер Internet Information Services (IIS), для поддержки веб-публикации и развертывания, используя службу удаленного агента средство веб-развертывания IIS (веб-развертывания).
> 
> При работе с Web Deploy версии 2.0 или более поздней версии, существует три основных подходов, которые можно использовать для получения приложений или узлов на веб-сервере. Можно выполнить следующие действия.
> 
> - Используйте *веб-развертывания службы удаленного агента*. Этот подход требует меньше конфигурация веб-сервера, но необходимо предоставить учетные данные администратора локального сервера для развертывания ничего на сервер.
> - Используйте *веб-развертывания обработчик*. Этот подход гораздо более сложна и требует больше усилий начальной для настройки веб-сервера. Тем не менее при использовании этого подхода можно настроить службы IIS, чтобы разрешить пользователям без прав администратора для выполнения развертывания. Веб-развертывание обработчик доступен только в службах IIS 7 или более поздней версии.
> - Используйте *автономное развертывание*. Этот подход требует минимальной конфигурации веб-сервера, но администратор сервера необходимо вручную скопировать веб-пакета на сервере и импортировать его в диспетчере IIS.
> 
> Дополнительные сведения о ключевых функций, преимущества и недостатки из этих подходов см. в разделе [Выбор оптимального подхода справа для веб-развертывания](choosing-the-right-approach-to-web-deployment.md).


## <a name="is-the-web-deploy-remote-agent-the-right-approach-for-you"></a>— Веб-развертывания удаленного агента правильного подхода для вас?

Да, если пользователь будет развернуть содержимое может предоставить учетные данные администратора на конечном сервере. Этот подход часто желательна для таких типов сценариев:

- Средах разработки и тестирования, если разработчик имеет полный контроль над веб-сервере назначения и сервер базы данных.
- Небольших организаций, в которых один пользователь или небольшой группе пользователей имеет контроль над весь жизненный цикл приложения.

В большом числе более крупных организациях, а особенно для промежуточной или рабочей среде не часто следует предоставить пользователям права администратора на веб-серверах. В случае размещенного веб-серверы это маловероятно особенно относится к. Кроме того Если вы планируете автоматизировать развертывание на сервере сборки, не можно использовать учетные данные администратора в процессе развертывания. В этих сценариях настройки веб-серверов для поддержки развертывания с использованием [веб-развертывания обработчик](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md) может предоставлять более разумный выбор.

## <a name="task-overview"></a>Общие сведения о задаче

В этом разделе описываются способы настройки веб-сервер Internet Information Services (IIS) 7.5, чтобы принять и развернуть веб-пакеты на удаленном компьютере, используя подход агента веб-развертывания удаленного. Вам потребуется:

- Установите службы IIS 7.5 и IIS 7, рекомендуемая конфигурация.
- Установите веб-развертывания 2.1 или более поздней версии.
- Создайте веб-сайт IIS для размещения развернутым содержимым.
- Убедитесь, что запущена служба агента веб-развертывания.

Для размещения примера решения, в частности, вам также потребуется:

- Установите платформу .NET 4.0.
- Установите ASP.NET MVC 3.

В этом разделе будет показано, как для выполнения каждой из этих процедур. Задачи и пошаговые руководства в этом разделе предполагается, вы начинаете с построением чистого сервера под управлением Windows Server 2008 R2. Прежде чем продолжить, убедитесь, что:

- Windows Server 2008 R2 с пакетом обновления 1 и все доступные обновления установлены.
- Сервер присоединен к домену.
- Сервер имеет статический IP-адрес.

> [!NOTE]
> Дополнительные сведения о присоединении к домену компьютеров см. в разделе [присоединение компьютеров к домену и вход](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx). Дополнительные сведения о настройке статических IP-адресов см. в разделе [настроить статический IP-адрес](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx). Служба удаленного агента поддерживается начиная с IIS 6 и не требует присоединенных к домену. Тем не менее действия, описанные в этом учебнике, созданы и протестированы на IIS 7.5 и процедуры для других версий может отличаться.


## <a name="install-products-and-components"></a>Установите продукты и компоненты

Этот раздел поможет выполнить установку необходимых продуктов и компонентов на веб-сервере. Прежде чем начать, рекомендуется запустить Центр обновления Windows для убедитесь, что сервер находится в актуальное.

В этом случае необходимо установить следующее:

- **Рекомендуемая конфигурация IIS 7**. Это позволяет **веб-сервер (IIS)** роли веб-сервера и устанавливает набор модулей IIS и компоненты, необходимые для размещения приложения ASP.NET.
- **.NET framework 4.0**. Это необходимо для запуска приложений, которые были созданы в этой версии платформы .NET Framework.
- **Веб-средство развертывания 2.1 или более поздней версии**. При этом устанавливаются на сервере веб-развертывания (и его базовых исполняемого файла, MSDeploy.exe). В рамках этого процесса он устанавливает и запускает службу агента веб-развертывания. Эта служба позволяет развертывать веб-пакеты с удаленного компьютера.
- **ASP.NET MVC 3**. При этом устанавливаются сборки, необходимые для запуска приложения MVC 3.

> [!NOTE]
> В этом пошаговом руководстве описывается использование установщика веб-платформы для установки и настройки необходимых компонентов. Несмотря на то, что нет необходимости использовать установщик веб-платформы, оно упрощает процесс установки автоматически обнаруживая зависимости и обеспечивая всегда получать последние версии продукта. Дополнительные сведения см. в разделе [платформы установщика Microsoft Web 3.0](https://go.microsoft.com/?linkid=9805118).


**Для установки необходимых продуктов и компонентов**

1. Загрузите и установите [Web Platform Installer](https://go.microsoft.com/?linkid=9805118).
2. По завершении установки автоматически запустит установщик веб-платформы.

    > [!NOTE]
    > Теперь вы можете запускать установщика веб-платформы в любое время с **запустить** меню. Для этого на **запустить** меню, нажмите кнопку **все программы**, а затем нажмите кнопку **Microsoft Web Platform Installer**.
3. В верхней части **веб-платформы 3.0 установщика** окно, нажмите кнопку **продуктов**.
4. В левой части окна в области навигации выберите **платформ**.
5. В **Microsoft .NET Framework 4** строк, если платформа .NET Framework не установлена, нажмите кнопку **добавить**.

    > [!NOTE]
    > Может уже установлена платформа .NET Framework 4.0 через Центр обновления Windows. Если уже установлен продукт или компонент, установщик веб-платформы сообщит об этом, заменив **добавить** кнопка с текстом **установленные**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image1.png)
6. В **ASP.NET MVC 3 (Visual Studio 2010)** щелкните **добавить**.
7. В области навигации щелкните **сервера**.
8. В **конфигурации рекомендуется IIS 7** щелкните **добавить**.
9. В **2.1 средство развертывания веб** щелкните **добавить**.
10. Нажмите кнопку **Установить**. Установщик веб-платформы будет отображен список продуктов & #x 2014; а также связанные с ним зависимости & #x 2014; должны быть установлены и попросит принять условия лицензионного соглашения.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image2.png)
11. Ознакомьтесь с условиями лицензии и, если вы согласны с условиями, щелкните **принимаю**.
12. После завершения установки нажмите кнопку **Готово**, а затем закройте **веб-платформы 3.0 установщика** окна.

При установке .NET Framework 4.0 до установки служб IIS, необходимо будет выполнить [средство регистрации IIS ASP.NET](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (aspnet\_regiis.exe) для регистрации последнюю версию ASP.NET в IIS. Если этого не сделать, обнаружится, что IIS будет обслуживать статическое содержимое (например, HTML-файлы) без проблем, но он будет возвращать **404.0 Ошибка HTTP — не удалось найти** при попытке перейти к содержимому ASP.NET. Эту процедуру можно использовать, чтобы зарегистрировать ASP.NET 4.0.

**Чтобы зарегистрировать ASP.NET 4.0 в IIS**

1. Нажмите кнопку **запустить**, а затем введите **командной строки**.
2. В результатах поиска щелкните правой кнопкой мыши **командной строки**, а затем нажмите кнопку **Запуск от имени администратора**.
3. В окне командной строки перейдите к **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** каталога.
4. Введите следующую команду и нажмите клавишу ВВОД:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-remote-agent/samples/sample1.cmd)]
5. Если вы планируете размещения веб-64-разрядных приложений в любой момент, должны также зарегистрировать 64-разрядной версии ASP.NET в IIS. Чтобы сделать это, в окне командной строки, перейдите в папку **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** каталога.
6. Введите следующую команду и нажмите клавишу ВВОД:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-remote-agent/samples/sample2.cmd)]

Как рекомендуется используйте Центр обновления Windows еще раз на этом этапе для загрузки и установки доступных обновлений для новых продуктов и компонентов, которые после установки.

## <a name="configure-the-iis-website"></a>Настройка веб-сайта IIS

Перед развертыванием веб-содержимого на сервер, необходимо создать и настроить веб-сайт IIS для размещения содержимого. Веб-развертывания можно развернуть только веб-пакеты в существующих веб-сайт IIS; не удается создать веб-сайт для вас. На высоком уровне необходимо выполнить следующие задачи:

- Создайте папку в файловой системе для размещения контента.
- Создайте веб-сайт IIS для предоставления содержимого и связать его с локальной папкой.
- Предоставление разрешения на чтение для удостоверения пула приложений в локальную папку.

Несмотря на то, что ничто не мешает вы из развертывания содержимого на веб-сайт по умолчанию в службах IIS, такой подход не рекомендуется для ничего, кроме тест или Демонстрация сценариев. Для имитации рабочей среде, необходимо создать новый веб-сайт IIS с параметрами, которые относятся к требованиям приложения.

**Для создания и настройки веб-сайт IIS**

1. Создайте папку для хранения содержимого на локальной файловой системы (например, **C:\DemoSite**).
2. На **запустить** последовательно выберите пункты **Администрирование**, а затем нажмите кнопку **Диспетчер Internet Information Services (IIS)**.
3. В диспетчере служб IIS в **подключений** панели, раскройте узел сервера (например, **TESTWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image3.png)
4. Щелкните правой кнопкой мыши **сайтов** , а затем щелкните **добавить веб-узел**.
5. В **имя сайта** введите имя для веб-сайта IIS (например, **DemoSite**).
6. В **физический путь** введите (или перейдите к) путь к локальной папке (например, **C:\DemoSite**).
7. В **порт** введите номер порта, на котором будет размещаться веб-сайт (например, **85**).

    > [!NOTE]
    > Стандартные порты — 80 для HTTP и порт 443 для HTTPS. Тем не менее если размещать этот веб-узел по порту 80, необходимо остановить веб-сайт по умолчанию, чтобы получить доступ к веб-узла.
8. Оставить **имя узла** поле пустым, если вы хотите настроить запись доменных имен (DNS) для веб-сайта и нажмите кнопку **ОК**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image4.png)

    > [!NOTE]
    > В рабочей среде можно разместить веб-сайт с портом 80 и Настройка заголовка узла, а также соответствующие записи DNS. Дополнительные сведения о настройке заголовков узлов в IIS 7 см. в разделе [Настройка заголовка узла для веб-сайта (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx). Дополнительные сведения о роли DNS-сервера в Windows Server 2008 R2 см. в разделе [Обзор DNS-сервера](https://technet.microsoft.com/en-gb/library/cc770392.aspx) и [DNS-сервер](https://technet.microsoft.com/windowsserver/dd448607).
9. В области **Действия** в разделе **Изменение веб-сайта**выберите пункт **Привязки**.
10. В **привязки сайта** диалоговое окно, нажмите кнопку **добавить**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image5.png)
11. В **Добавление привязки сайта** диалоговое окно, набор **IP-адрес** и **порт** согласно конфигурации существующего сайта.
12. В **имя узла** введите имя веб-сервера (например, **TESTWEB1**), а затем нажмите кнопку **ОК**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image6.png)

    > [!NOTE]
    > Первой привязки сайта дает возможность доступа к сайту, локально с помощью IP-адрес и порт или `http://localhost:85`. Вторая привязка сайта дает возможность доступа к сайту с других компьютеров в домене с помощью имени компьютера (например, http://testweb1:85).
13. В **привязки сайта** диалоговое окно, нажмите кнопку **закрыть**.
14. В **подключений** области, нажмите кнопку **пулы приложений**.
15. В **пулы приложений** щелкните правой кнопкой мыши имя пула приложений и нажмите кнопку **основные параметры**. По умолчанию имя пула приложений будет соответствовать имени веб-сайта (например, **DemoSite**).
16. В **версии платформы .NET Framework** выберите **v4.0.30319 .NET Framework**, а затем нажмите кнопку **ОК**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image7.png)

    > [!NOTE]
    > Пример решения требуется .NET Framework 4.0. Это не является обязательным для веб-развертывания в целом.

Чтобы веб-сайта для предоставления содержимого удостоверение пула приложений должны быть разрешения на локальную папку, в которой хранится содержимое. В IIS 7.5 пулы приложений выполняются с уникальным пула приложений по умолчанию (в отличие от предыдущих версий служб IIS, где пулы приложений будут обычно выполняются с помощью учетной записи сетевой службы). Удостоверение пула приложений не является учетной записью реальных пользователей и не отображается на любые списки пользователей или групп & #x 2014; вместо этого он создается динамически при запуске пула приложений. Каждый удостоверение пула приложений добавляется в локальную **IIS\_IUSRS** группы безопасности, как скрытый элемент.

Чтобы предоставить разрешения для удостоверения пула приложений для файла или папки, имеются две возможности:

- Назначить разрешения для удостоверения пула приложений непосредственно, в формате **пул приложений IIS\***[имя пула приложений] * (например, **IIS AppPool\DemoSite**).
- Назначение разрешений для **IIS\_IUSRS** группы.

Наиболее распространенным подходом является назначение разрешений локальной **IIS\_IUSRS** группировать, так как такой подход позволяет изменять пулы приложений без перенастройки разрешения файловой системы. В следующей процедуре используется этот подход на основе группы.

> [!NOTE]
> Дополнительные сведения о удостоверений пула приложений в IIS 7.5 см. в разделе [удостоверений пула приложений](https://go.microsoft.com/?linkid=9805123).


**Чтобы настроить разрешения для веб-сайта IIS**

1. В проводнике Windows перейдите в папку локальной папке.
2. Щелкните правой кнопкой мыши папку и нажмите кнопку **свойства**.
3. На **безопасности** щелкните **изменить**, а затем нажмите кнопку **добавить**.
4. Нажмите кнопку **расположения**. В **расположения** диалоговое окно, выберите локальный сервер и нажмите кнопку **ОК**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image8.png)
5. В **Выбор пользователей или групп** диалоговом **IIS\_IUSRS**, нажмите кнопку **проверить имена**, а затем нажмите кнопку **ОК**.
6. В **разрешения для *** [имя папки]*диалоговое окно, обратите внимание, что назначить новую группу **чтения &amp; выполнение**, **список содержимого папки**, и **Чтения** разрешения по умолчанию. Оставьте без изменений и нажмите кнопку **ОК**.
7. Нажмите кнопку **ОК** закрыть *[имя папки] *** свойства** диалоговое окно.

Как последняя задача перед попыткой развертывания любой веб-пакетов на сервере, вам следует что запущена служба агента веб-развертывания. При развертывании пакета на удаленном компьютере службу агента веб-развертывания отвечает за извлечение и установка содержимого пакета. Служба запускается по умолчанию при установке средства веб-развертывания и выполняется с удостоверением сетевой службы.

Можно проверить запущена ли служба несколькими различными способами, с помощью различных служебные программы командной строки и командлетов Windows PowerShell. Эта процедура описывает простой подход на основе пользовательского интерфейса.

**Чтобы проверить, что запущена служба агента веб-развертывания**

1. Нажмите кнопку **Пуск** , выберите команду **Администрирование**, а затем выберите **Службы**.
2. Найдите **службы агента веб-развертывания** строк и убедитесь, что **состояние** равно **Started**.

    ![](configuring-a-web-server-for-web-deploy-publishing-remote-agent/_static/image9.png)
3. Если служба еще не запущена, щелкните **запустить**.

## <a name="configure-firewall-exceptions"></a>Настройка исключений брандмауэра

По умолчанию служба удаленного агента прослушивает TCP-порт 80, на этот URL-адрес:

http:// [*имя сервера*] / MSDEPLOYAGENTSERVICE

В большинстве случаев не требуется настроить все правила дополнительный брандмауэр для службы удаленного агента, поскольку веб-серверы обычно прослушивания HTTP-запросов через порт 80. При стандартной установке на нестандартный порт, необходимо настроить исключения брандмауэра при необходимости.

## <a name="conclusion"></a>Заключение

На этом этапе веб-сервер готов принять и установить веб-пакеты с удаленного компьютера. Перед попыткой развертывания веб-приложения на сервере, можно проверить следующие факты:

- Зарегистрировали ASP.NET 4.0 в IIS?
- Удостоверение пула приложений имеют доступ на чтение к папке источника для веб-сайта
- Запущена служба агента веб-развертывания

## <a name="further-reading"></a>Дополнительные сведения

Рекомендации по настройке пользовательских файлов проекта Microsoft Build Engine (MSBuild) для развертывания веб-пакеты с удаленной службой агента см. в разделе [настройки свойств развертывания для целевой среды](configuring-deployment-properties-for-a-target-environment.md).

>[!div class="step-by-step"]
[Назад](scenario-configuring-a-production-environment-for-web-deployment.md)
[Вперед](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler.md)
