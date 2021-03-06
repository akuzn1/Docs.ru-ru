---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment
title: "Выбор правильного подхода к веб-развертывание | Документы Microsoft"
author: jrjlee
description: "При работе с Internet Information Services (IIS) средство веб-развертывания (Web Deploy) 2.0 или более поздней версии, существует три основных подходов, которые можно использовать для получения..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/04/2012
ms.topic: article
ms.assetid: 787a53fd-9901-4a11-9d58-61e0509cda45
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/choosing-the-right-approach-to-web-deployment
msc.type: authoredcontent
ms.openlocfilehash: b77aa37160f3822f58908866e44497aea3d3bdc8
ms.sourcegitcommit: 493a215355576cfa481773365de021bcf04bb9c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
---
<a name="choosing-the-right-approach-to-web-deployment"></a>Выбор правильного подхода к веб-развертывания
====================
по [Джейсон Lee](https://github.com/jrjlee)

[Скачать PDF](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> При работе с Internet Information Services (IIS) средство веб-развертывания (Web Deploy) 2.0 или более поздней версии, существует три основных подходов, которые можно использовать для получения упакованных веб-приложений на веб-сервере. Вы можете сделать следующее.
> 
> - Развертывание приложения на удаленном компьютере, обращаясь к *службы агента веб-развертывания* (также называется «удаленного агента») на целевом сервере.
> - Развертывание приложения из удаленного расположения с помощью Web развертывания по запросу (также называется «temp агента»).
> - Развертывание приложения на удаленном компьютере, обращаясь к *обработчик развертывания IIS Web* на целевом сервере.
> - Разверните приложение, вручную копирование веб-пакета на конечный сервер и импортировав их в диспетчере IIS.
> 
> Как настроить веб-серверах назначения будет зависеть от какой подход к развертыванию, вы хотите использовать. Этот раздел поможет вам решить, какой подход к развертыванию подходит для вас.


Эта таблица показывает основные преимущества и недостатки каждого подхода развертывания, а также сценарии, которые чаще всего соответствия каждого подхода.

| Подход | Преимущества | Недостатки | Типичные сценарии |
| --- | --- | --- | --- |
| Удаленный агент | Его можно легко настроить. Он подходит для регулярного обновления для веб-приложения и содержимого. | Пользователь должен иметь права администратора на целевом сервере. Пользователь не может указать альтернативные учетные данные. | Среды разработки. Тестовую среду. |
| Временный агента | Нет необходимости Установка веб-развертывания на конечном компьютере. Автоматически используется последняя версия веб-развертывания. | Пользователь должен иметь права администратора на целевом сервере. Пользователь не может указать альтернативные учетные данные. | Среды разработки. Тестовую среду. |
| Веб-развертывание обработчика | Пользователи без прав администратора могут развертывать содержимое. Он подходит для регулярного обновления для веб-приложения и содержимого. | Это гораздо сложнее, для настройки. | Промежуточные среды. Интрасети рабочих средах. Что касается размещенных сред. |
| Автономное развертывание | Это очень легко настроить. Он подходит для изолированных средах. | Администратор сервера необходимо вручную скопировать и импортировать веб-пакета, каждый раз. | С выходом в Интернет рабочих средах. Изолированное сетевых сред. |
  

## <a name="using-the-remote-agent"></a>С помощью удаленного агента

При установке веб-развертывания с использованием параметров по умолчанию на конечном сервере службу агента веб-развертывания («удаленного агента») автоматически устанавливается и запущен. По умолчанию удаленный агент предоставляет конечную точку HTTP по этому адресу:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample1.cmd)]


> [!NOTE]
> Можно заменить [*сервера*] с именем компьютера веб-сервера, IP-адрес для веб-сервера или имени узла, разрешающее на веб-сервер.


Администраторы сервера можно развернуть веб-пакетов в удаленном расположении, например компьютере разработчика или на сервере сборки, с указанием адреса этой конечной точки. Например предположим, что Hink Мэтт компании Fabrikam, Inc. создала проект ContactManager.Mvc веб-приложения на своем компьютере разработчика. Процесс построения создает веб-пакет вместе с *. deploy.cmd* файл, содержащий команды веб-развертывания, необходимые для установки пакета. Если Мэтт является администратором сервера на сервере TESTWEB1, он может разворачивать веб-приложения к серверу веб-тест, запустив следующую команду на машине разработчика:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample2.cmd)]


Фактический фактов исполняемый файл веб-развертывания могут определять адрес конечной точки удаленного агента при указании имени компьютера, Мэтт достаточно просто ввести следующее:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample3.cmd)]


> [!NOTE]
> Дополнительные сведения о синтаксисе командной строки на веб-развертывания и *. deploy.cmd* файлов, в разделе [как: установка, развертывание пакета с помощью deploy.cmd файл](https://msdn.microsoft.com/library/ff356104.aspx).


Удаленный агент предлагает простой способ развертывания содержимого из удаленного расположения, и этот подход можно работать с одним щелчком или автоматического развертывания. Тем не менее пользователь, выполняющий команду развертывания необходимо также администратором домена или членом группы локальных администраторов на конечном сервере. Кроме того удаленный агент не поддерживает обычную проверку подлинности, поэтому невозможно передать альтернативные учетные данные в командной строке.

Развертывание в разработки или тестирования сценариев, где нередко разработчикам контролировать с полными правами администратора в тестовой среде сервера и приложений обычно повторного создания и развертывания очень полезна предоставляет удаленного агента часто. Однако такой подход обычно менее приемлемым для промежуточной или рабочей среде.

Пример сквозной сценарий, использующий подход с использованием удаленного агента. в разделе [сценарий: Настройка тестовой среды для веб-развертывания](scenario-configuring-a-test-environment-for-web-deployment.md).

## <a name="using-the-temp-agent"></a>С помощью временного агента

Подход с использованием агента temp для развертывания аналогичен подход с использованием удаленного агента. Однако в отличие от подход удаленного агента, не требуется установка веб-развертывания на веб-сервере назначения. Вместо этого при выполнении развертывания веб-развертывания установит временной версии служба агента веб-развертывания на конечном сервере и будет использовать его для развертывания содержимого на IIS. После завершения развертывания все временные файлы будут удалены.

Если вы хотите использовать параметр поставщика временных агента, добавьте **/g** флаг для вашей команды развертывания:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample4.cmd)]


> [!NOTE]
> Нельзя использовать агент временной Если службу агента веб-развертывания устанавливается на конечном компьютере, даже если служба не работает.


Преимущество такого подхода является не требуется поддерживать установки веб-развертывания на целевом серверах. Кроме того нет необходимости убедитесь, что исходный и конечный компьютеры под управлением той же версии веб-развертывания. Однако у этого подхода есть же основной ограничения, что подход удаленного агента, а именно: для развертывания содержимого должен быть локальным администратором на конечном сервере, что поддерживается только проверка подлинности NTLM. Подход с использованием временной агент также требуется более начальной конфигурации целевой среде.

Дополнительные сведения об использовании агента temp в разделе [как: установка, развертывание пакета с помощью файла deploy.cmd](https://msdn.microsoft.com/library/ff356104.aspx) и [развертывание Web по запросу](https://technet.microsoft.com/library/ee517345(WS.10).aspx).

## <a name="using-the-web-deploy-handler"></a>Использование веб-развертывание обработчика

Начиная со службами IIS 7 веб-развертывания подход Альтернативное развертывание через обработчик развертывания IIS Web. Веб-развертывание обработчик тесно интегрирован с IIS службы веб-управления (WMSvc), который позволяет пользователям управлять веб-узлы IIS из удаленных расположений.

По умолчанию удаленный агент предоставляет конечную точку HTTP по этому адресу:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample5.cmd)]


> [!NOTE]
> Можно заменить [*сервера*] с именем компьютера веб-сервера, IP-адрес для веб-сервера или имени узла, разрешающее на веб-сервер.


Большим преимуществом веб-развертывания обработчик через удаленный агент и агент temp — настроить службы IIS, чтобы разрешить пользователям без прав администратора для развертывания приложения и содержимое на определенных веб-сайтов IIS. Веб-развертывание обработчик также поддерживает обычную проверку подлинности, чтобы обеспечить альтернативные учетные данные как параметры в командах веб-развертывания. Основной недостаток является изначально гораздо более сложной установки и настройки веб-развертывания обработчик.

В случае пользователей без прав администратора службы веб-управления (WMSvc) допускает только пользователю подключаться к серверу IIS с помощью подключения уровня веб-сайта, а не соединение уровня сервера. Для доступа к определенному сайту, можно включить строку запроса конкретного узла в адресе конечной точки:


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample6.cmd)]


Например предположим, что процесс построения настроена для автоматического развертывания веб-приложения в промежуточной среде при каждом успешном построении. Если используется подход с использованием удаленного агента, необходимо сделать удостоверение процесса построения администратором на конечном серверах. В отличие от этого с помощью веб-развертывания обработчик подход можно присвоить пользователя без прав администратора & #x 2014; **FABRIKAM\stagingdeployer** в этом случае & #x 2014; разрешение для определенного веб-сайта IIS только и процессом сборки можно предоставить эти учетные данные для развертывания веб-пакета.


[!code-console[Main](choosing-the-right-approach-to-web-deployment/samples/sample7.cmd)]


> [!NOTE]
> Дополнительные сведения о веб-развертывания операции командной строки и синтаксис см. в разделе [веб-развертывания ссылку командной строки](https://technet.microsoft.com/library/dd568991(v=ws.10).aspx). Дополнительные сведения об использовании *. deploy.cmd* файла см. в разделе [как: установка, развертывание пакета с помощью deploy.cmd файл](https://msdn.microsoft.com/library/ff356104.aspx).


Веб-развертывание обработчик предоставляет полезна для развертывания в промежуточной среды, среде выполнения и интранет-рабочих средах, где доступен удаленного доступа к серверу, но не используются учетные данные администратора.

Пример конца в конец сценарий, использующий веб-развертывания обработчик подход, в разделе [сценарий: Настройка среды промежуточного хранения для развертывания веб-](scenario-configuring-a-staging-environment-for-web-deployment.md).

## <a name="using-offline-deployment"></a>С помощью автономного развертывания

В некоторых случаях это не возможно или целесообразно для развертывания приложения и содержимое на веб-сайт IIS на удаленном компьютере. Например исходный и конечный компьютеры может находиться в изолированных сетях или сегменты сети или политика брандмауэра не может разрешить удаленный доступ.

В сценариях они по-прежнему использовать упаковка и публикация возможностей веб-развертывания; только что их нельзя использовать из удаленного расположения. Вместо этого администратор на целевом сервере необходимо скопировать веб-пакета на сервере и импортировать его в диспетчере IIS.

![](choosing-the-right-approach-to-web-deployment/_static/image1.png)

Автономное развертывание подход обычно используется в производственной среде с выходом в Интернет, где серверы в сети периметра может быть ограничен связь с компьютерами во внутренней сети.

Пример сквозной сценарий, использующий подход автономное развертывание. в разделе [сценарий: Настройка рабочей среде для развертывания веб-](scenario-configuring-a-production-environment-for-web-deployment.md).

## <a name="further-reading"></a>Дополнительные сведения

Дополнительные сведения о веб-развертывания операции командной строки и синтаксис см. в разделе [веб-развертывания ссылку командной строки](https://technet.microsoft.com/library/dd568991(v=ws.10).aspx). Дополнительные сведения об использовании *. deploy.cmd* файла см. в разделе [как: установка, развертывание пакета с помощью deploy.cmd файл](https://msdn.microsoft.com/library/ff356104.aspx).

Общие рекомендации по различными способами, в котором можно развернуть веб-пакеты с удаленного компьютера см. в разделе [с помощью Web развертывание удаленно](https://technet.microsoft.com/library/ee461175(WS.10).aspx). Дополнительные сведения об использовании развертывания Web по запросу см. в разделе [развертывание Web по запросу](https://technet.microsoft.com/library/ee517345(WS.10).aspx).

>[!div class="step-by-step"]
[Назад](configuring-server-environments-for-web-deployment.md)
[Вперед](scenario-configuring-a-test-environment-for-web-deployment.md)
