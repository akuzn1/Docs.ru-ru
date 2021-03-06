---
uid: mvc/overview/getting-started/introduction/adding-a-controller
title: "Добавление контроллера | Документы Microsoft"
author: Rick-Anderson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: cc764f3b-6921-486a-8f44-c6ccd1249acd
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-controller
msc.type: authoredcontent
ms.openlocfilehash: c8f317b2ac133f560461917af1588b7a1fa51c4f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
---
<a name="adding-a-controller"></a>Добавление контроллера
====================
По [Рик Андерсон](https://github.com/Rick-Anderson)

[!INCLUDE[Tutorial Note](sample/code-location.md)]

Обозначает MVC *model-view-controller*. MVC представляет собой шаблон для разработки приложений, которые хорошо архитектурой, тестирования и простые в обслуживании. Содержать MVC-приложений:

- **M** odels: классы, представляющие данные приложения, которые используют логику проверки для применения бизнес-правил для этих данных.
- **V** iews: файлы шаблонов, которые приложение использует для динамического создания HTML-ответы.
- **C** ontrollers: классы, которые обрабатывают входящие запросы браузера, получения данных модели, а затем укажите шаблонов представлений, которые возвращают ответа в браузер.

Мы быть, охватывающие все эти компоненты этого учебника ряда и показано, как их использовать для построения приложения.

> [!NOTE]
> Шаблон был выбран на предыдущем шаге MVC по умолчанию. Это главная, создает учетную запись и управление контроллерами по умолчанию.

Давайте начнем с создания класса контроллера. В **обозревателе решений**, щелкните правой кнопкой мыши *контроллеров* папку и нажмите кнопку **добавить**, затем **контроллера**.


![](adding-a-controller/_static/image1.png)

В **Добавление формирования шаблонов** диалоговое окно, нажмите кнопку **контроллер MVC 5 - пустой**, а затем нажмите кнопку **добавить**.

![](adding-a-controller/_static/image2.png)  
 

Имя вашего нового контроллера «HelloWorldController» и нажмите кнопку **добавить**.

![Добавление контроллера](adding-a-controller/_static/image3.png)

Обратите внимание на **обозревателе решений** что файл был создан именованный *HelloWorldController.cs* и ее *Views\HelloWorld*. Контроллер открыт в Интегрированной среде разработки.

![](adding-a-controller/_static/image4.png)

Замените содержимое файла следующим кодом.

[!code-csharp[Main](adding-a-controller/samples/sample1.cs)]

Методы контроллера возвратит строку HTML-кода в качестве примера. Контроллер называется `HelloWorldController` и первый метод называется `Index`. Давайте вызывать его из браузера. Запустите приложение (нажмите клавишу F5 или Ctrl + F5). В браузере, добавьте &quot;HelloWorld&quot; пути в адресной строке. (Например, на рисунке ниже, он является `http://localhost:1234/HelloWorld.`) будет выглядеть страница в браузере, а следующий снимок экрана. В методе выше код непосредственно вернул строку. Вы сообщили система просто возвращается HTML-ФАЙЛ, как и!

![](adding-a-controller/_static/image5.png)

ASP.NET MVC вызывает другой контроллер классы (и другие методы действия в них), в зависимости от входящего URL-адреса. Логику маршрутизации URL-адрес по умолчанию используется ASP.NET MVC использует формат следующим образом, чтобы определить, какой код для вызова:

`/[Controller]/[ActionName]/[Parameters]`

Задание формата для маршрутизации в *приложения\_Start/RouteConfig.cs* файла.

[!code-csharp[Main](adding-a-controller/samples/sample2.cs?highlight=7-8)]

При запуске приложения и не указывайте все сегменты URL-адреса, по умолчанию используется контроллер «Home» и «Индекс» метода действия, указанные в разделе значения по умолчанию приведенного выше кода.

Первая часть URL-адреса определяет класс контроллера для выполнения. Поэтому */HelloWorld* сопоставляется `HelloWorldController` класса. Во второй части URL-адрес определяет на класс для выполнения метода действия. Поэтому */HelloWorld/индекс* вызовет `Index` метод `HelloWorldController` класса для выполнения. Обратите внимание, что нам пришлось перейдите к */HelloWorld* и `Index` метод использовался по умолчанию. Это так, как метод с именем `Index` метод по умолчанию, который будет вызываться на контроллере, если тип не задан явно. В третьей части сегмента URL-адреса (`Parameters`) указываются данные маршрута. Мы рассмотрим данные о маршруте позже в этом учебнике.

Перейдите по адресу `http://localhost:xxxx/HelloWorld/Welcome`. `Welcome` Метод выполняется и возвращает строку, которая &quot;это метод приветствия действие... &quot;. Сопоставление по умолчанию MVC `/[Controller]/[ActionName]/[Parameters]`. Для этого URL-адреса заданы контроллер `HelloWorld` и метод действия `Welcome`. Часть URL-адреса `[Parameters]` на данный момент еще не использовалась.

![](adding-a-controller/_static/image6.png)

Давайте немного изменить этот пример, чтобы некоторые сведения о параметрах можно передать URL-адреса контроллера (например, */HelloWorld/приветствия? имя = Скотт&amp;numtimes = 4*). Изменение вашей `Welcome` метод, чтобы включить два параметра, как показано ниже. Обратите внимание, что код использует функцию C# необязательный параметр указывает, что `numTimes` параметр по умолчанию 1, если не передаются значения для этого параметра.

[!code-csharp[Main](adding-a-controller/samples/sample3.cs)]

> [!NOTE]
> Примечание по безопасности: Код выше используется [HttpUtility.HtmlEncode](https://msdn.microsoft.com/library/ee360286(v=vs.110).aspx) для защиты приложения от злонамеренного ввода данных (а именно JavaScript). Дополнительные сведения см. [как: защита от скриптов в веб-приложения путем применения HTML-кодирования строки](https://msdn.microsoft.com/library/a2a4yykt(v=vs.100).aspx).


 Запустите приложение и откройте пример URL-адрес (`http://localhost:xxxx/HelloWorld/Welcome?name=Scott&numtimes=4`). Можно попробовать разные значения для `name` и `numtimes` в URL-АДРЕСЕ. [Система привязки модели ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) автоматически сопоставляет именованные параметры из строки запроса в адресной строке для параметров в методе.

![](adding-a-controller/_static/image7.png)

В примере выше сегмента URL-адреса ( `Parameters`) не используется, `name` и `numTimes` параметры передаются как [строки запросов](http://en.wikipedia.org/wiki/Query_string). Подстановочный знак ? (вопросительный знак) в приведенном выше URL-адрес является знаком-разделителем и выполните строки запросов. Символ &amp; разделяет строки запроса.

Замените метод приветствия следующим кодом:

[!code-csharp[Main](adding-a-controller/samples/sample4.cs)]

Запустите приложение и введите следующий URL-адрес:`http://localhost:xxx/HelloWorld/Welcome/1?name=Scott`

![](adding-a-controller/_static/image8.png)

Это время третьего сегмента URL-адрес соответствует параметра маршрута `ID.` `Welcome` метод действия содержит параметр (`ID`), соответствующие спецификации URL-адрес в `RegisterRoutes` метод.

[!code-csharp[Main](adding-a-controller/samples/sample5.cs?highlight=7)]

В приложениях ASP.NET MVC обычно передавать параметры как маршрутизации данных (как мы сделали с Идентификатором выше), чем передача в качестве строки запроса. Можно также добавить маршрут для передачи и `name` и `numtimes` в параметрах как данные о маршруте в URL-АДРЕСЕ. В *приложения\_Start\RouteConfig.cs* файла, добавление маршрута «Hello»:

[!code-csharp[Main](adding-a-controller/samples/sample6.cs?highlight=13-16)]

Запустите приложение и перейдите к `/localhost:XXX/HelloWorld/Welcome/Scott/3`.

![](adding-a-controller/_static/image9.png)

Для многих приложений MVC маршрут по умолчанию работает нормально. Вы узнаете далее в этом учебнике для передачи данных с использованием связывателя модели, и вам не придется изменять маршрут по умолчанию для этого.

В этих примерах контроллер предоставляет &quot;VC&quot; часть MVC — то есть, представления и контроллера работы. Контроллер вернул HTML напрямую. Обычно вы не хотите контроллеров, возвращение HTML напрямую, так как становится очень сложно в код. Вместо этого мы обычно используем отдельное представление файла шаблона для создания HTML-ответа. Давайте посмотрим, далее в том, как можно это сделать.

>[!div class="step-by-step"]
[Назад](getting-started.md)
[Вперед](adding-a-view.md)
