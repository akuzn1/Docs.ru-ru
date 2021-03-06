---
uid: web-forms/overview/presenting-and-managing-data/model-binding/integrating-jquery-ui
title: "Интеграция Datepicker пользовательского интерфейса JQuery с привязкой модели и веб-форм | Документы Microsoft"
author: tfitzmac
description: "Этот учебник ряд демонстрирует основные аспекты использования привязки модели в проекте веб-форм ASP.NET. Привязка модели позволяет взаимодействия с данными дополнительные прямые-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 3cbab37b-fb0f-4751-9ec4-74e068c3f380
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/integrating-jquery-ui
msc.type: authoredcontent
ms.openlocfilehash: da3c8f347a709a4c9a47fd0ecce5201d9b0cd1b1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
<a name="integrating-jquery-ui-datepicker-with-model-binding-and-web-forms"></a>Интеграция Datepicker пользовательского интерфейса JQuery с привязкой модели и веб-форм
====================
по [Tom FitzMacken](https://github.com/tfitzmac)

> Этот учебник ряд демонстрирует основные аспекты использования привязки модели в проекте веб-форм ASP.NET. Привязка модели позволяет взаимодействия с данными более прост, чем работе с данными источника объектов (например, элемент управления ObjectDataSource или SqlDataSource). Эта серия начинается с вводный материал и перемещает на следующих занятиях рассматривается более сложных понятиях.
> 
> Этот учебник демонстрирует добавление пользовательского интерфейса JQuery [Datepicker мини-приложение](http://jqueryui.com/datepicker/) веб-форму и использовать модель привязки для обновления базы данных с выбранного значения.
> 
> Этот учебник построен на проект, созданный в [первый](retrieving-data.md) и [второй](updating-deleting-and-creating-data.md) части ряда.
> 
> Вы можете [загрузки](https://go.microsoft.com/fwlink/?LinkId=286116) весь проект C# или Visual Basic. Загружаемый код работает с Visual Studio 2012 или Visual Studio 2013. Она использует шаблон Visual Studio 2012, который немного отличается от шаблона Visual Studio 2013, показанными в данном руководстве.


## <a name="what-youll-build"></a>Мы создадим

В этом учебнике вы сможете:

1. Добавление свойства в модели, чтобы записать Стьюдента Дата регистрации
2. Разрешить пользователю выбирать дату регистрации с помощью JQuery UI Datepicker мини-приложения
3. Принудительное применение правил проверки для Дата регистрации

Datepicker пользовательского интерфейса JQuery мини-приложение позволяет пользователям легко выберите дату из календаря, возникающего, когда пользователь взаимодействует с этим полем. Использование этого мини-приложения может быть удобнее для пользователей, вручную введя дату. Интеграция Datepicker мини-приложения в страницу, которая использует модель привязки для операций с данными требуется небольшой объем дополнительной работы.

## <a name="add-a-new-property-to-the-model"></a>Добавить новое свойство в модель

Во-первых, мы добавим **Datetime** свойство для обучения модели и перенести это изменение в базе данных. Откройте **UniversityModels.cs**и добавить в модель студента выделенный код.

[!code-csharp[Main](integrating-jquery-ui/samples/sample1.cs?highlight=16-18)]

**RangeAttribute** включается принудительное применение правил проверки для свойства. В этом учебнике предполагается, что Contoso университета была основана на 1-го января 2013 и поэтому ранней даты регистрации недопустимы.

В окне управления пакетами, добавьте миграции, выполнив команду **AddEnrollmentDate добавьте миграции**. Обратите внимание, что миграции кода добавляет новый столбец даты и времени в таблицу студента. В соответствии со значением, которое указано в RangeAttribute, добавьте значение по умолчанию для нового столбца, как показано в следующем коде выделенный.

[!code-csharp[Main](integrating-jquery-ui/samples/sample2.cs?highlight=11)]

Сохраните изменения в файл для миграции.

Необходимо повторно инициализировать данные. Таким образом, открыть **Configuration.cs** в папку Migrations и удалите или закомментируйте код в **начальное значение** метод. Сохраните и закройте файл.

Теперь выполните команду **базы данных обновления**. Обратите внимание, что столбец существует в базе данных, а также все существующие записи имеют значения по умолчанию для параметра EnrollmentDate.

## <a name="add-dynamic-controls-for-enrollment-date"></a>Добавление динамических элементов управления для Дата регистрации

Теперь вы добавите элементы управления для отображения и редактирования Дата регистрации. На этом этапе значение изменяется в текстовое поле. Далее в этом учебнике текстового поля изменится для выбора дат JQuery мини-приложения.

Во-первых, важно отметить, что не требуется вносить какие-либо изменения **AddStudent.aspx** файла. Элемент управления DynamicControl автоматически будет отображать новое свойство.

Откройте **Students.aspx**и добавьте следующий выделенный код.

[!code-aspx[Main](integrating-jquery-ui/samples/sample3.aspx?highlight=13)]

Запустите приложение и обратите внимание, что значение даты регистрации можно задать путем ввода даты. При добавлении нового студента:

![Дата установки](integrating-jquery-ui/_static/image1.png)

Или изменение существующего значения:

![Изменение даты](integrating-jquery-ui/_static/image2.png)

Введите дату работает, но он может быть обслуживание клиентов, которые вы хотите предоставить. В следующем разделе вы включите выбора даты по календарю.

## <a name="install-nuget-package-to-work-with-jquery-ui"></a>Установка пакета NuGet для работы с пользовательским Интерфейсом JQuery

**Пользовательского интерфейса соком** пакет NuGet позволяет легко интегрировать пользовательского интерфейса JQuery мини-приложений в веб-приложения. Чтобы использовать этот пакет, установите ее с помощью NuGet.

![Добавление пользовательского интерфейса соком](integrating-jquery-ui/_static/image3.png)

Версия соком пользовательского интерфейса, который вы устанавливаете может конфликтовать с версии JQuery в приложении. Перед продолжением работы с учебником попробуйте запустить приложение. При возникновении ошибки JavaScript необходимо согласовать версии JQuery. Можно добавить ожидаемая версия JQuery в папку скриптов (версия 1.8.2 во время написания этого учебника) или в Site.master, укажите путь к файлу JQuery.

[!code-aspx[Main](integrating-jquery-ui/samples/sample4.aspx)]

## <a name="customize-datetime-template-to-include-datepicker-widget"></a>Настройка шаблона даты и времени для включения Datepicker мини-приложения

Вы добавите мини-приложение Datepicker шаблон динамических данных для изменения значения даты и времени. Добавление мини-приложения в шаблон, он автоматически отображается в форме для добавления нового студента и в представлении сетки для редактирования учащихся. Откройте **DateTime\_Edit.ascx платформы**и добавьте следующий выделенный код.

[!code-aspx[Main](integrating-jquery-ui/samples/sample5.aspx?highlight=3)]

В файле кода установит дат минимальное и максимальное для DatePicker. Установив эти значения, позволит пользователям перейти к недопустимой датой. Вы получите минимальное и максимальное значения из **RangeAttribute** на свойства даты и времени, если таковой имеется. Откройте **DateTime\_Edit.ascx.cs**и добавьте следующий выделенный код на страницу\_метод Load.

[!code-csharp[Main](integrating-jquery-ui/samples/sample6.cs?highlight=9-14)]

Запустите веб-приложение и перейдите на страницу AddStudent. Укажите значения для полей и обратите внимание, что при нажатии кнопки в текстовом поле для регистрации дата календаря.

![Выбор даты](integrating-jquery-ui/_static/image4.png)

Выберите дату, а затем нажмите кнопку **вставить**. RangeAttribute принудительную проверку на сервере. Установив свойство minDate Datepicker, также применяются проверки на стороне клиента. Календарь не позволяющие переходить на дату до значение minDate.

При изменении записи в представлении в виде сетки, также отображается календарь.

![DatePicker в GridView](integrating-jquery-ui/_static/image5.png)

## <a name="conclusion"></a>Заключение

В этом учебнике вы узнали, как внедрить JQuery мини-приложения в веб-формы, использующей привязку модели.

В следующем [учебника](using-query-string-values-to-retrieve-data.md), будет использоваться значение строки запроса при выборке данных.

>[!div class="step-by-step"]
[Назад](sorting-paging-and-filtering-data.md)
[Вперед](using-query-string-values-to-retrieve-data.md)
