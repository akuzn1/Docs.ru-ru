---
uid: web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
title: "Создание и использование вспомогательного класса в ASP.NET Web Pages (Razor) сайта | Документы Microsoft"
author: tfitzmac
description: "В этой статье описывается создание вспомогательного класса в на веб-сайт ASP.NET Web Pages (Razor). Вспомогательный объект является компонентом для повторного использования, который включает код и разметку для производительности..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: 46bff772-01e0-40f0-9ae6-9e18c5442ee6
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 5d0c1ae09d8fbc91ff76cd4045d439abafee7736
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
<a name="creating-and-using-a-helper-in-an-aspnet-web-pages-razor-site"></a>Создание и использование вспомогательный объект в сайт ASP.NET Web Pages (Razor)
====================
по [Tom FitzMacken](https://github.com/tfitzmac)

> В этой статье описывается создание вспомогательного класса в на веб-сайт ASP.NET Web Pages (Razor). Объект *вспомогательный* повторно используемый компонент, который включает код и разметку, чтобы выполнить задачу, которая может оказаться трудоемкой и сложной.
> 
> **Что вы узнаете следующее.** 
> 
> - Как создать и использовать простой вспомогательный.
> 
> Существуют следующие функции ASP.NET, представленные в статье:
> 
> - `@helper` Синтаксиса.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Версии программного обеспечения, используемая в этом учебнике
> 
> 
> - Веб-страниц ASP.NET (Razor) 3
>   
> 
> Этот учебник также работает с веб-страницы ASP.NET 2.


## <a name="overview-of-helpers"></a>Общие сведения о вспомогательных методов

Если необходимо выполнять те же задачи на разных страницах веб-узла, можно использовать вспомогательный класс. Веб-страниц ASP.NET включает ряд вспомогательных методов, и существует много других, которые можно загрузить и установить. (Список встроенных вспомогательных методов веб-страницах ASP.NET, перечислен в [краткий справочник по API ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202907).) Если ни один из существующих вспомогательные методы отвечают вашим требованиям, можно создать собственный вспомогательный.

Вспомогательный класс позволяет использовать общий блок кода на нескольких страницах. Предположим, что на странице часто требуется создать элемент заметки, которая устанавливается отдельно от обычные абзацы. Возможно Примечание создается как `<div>` элемент, имеет стиль поле с границей. А не добавить этот же разметки страницы каждый раз будет отображаться заметки, можно упаковать разметку в качестве вспомогательного класса. Можно вставить примечание с единой строки кода в любом месте он нужен.

Использование вспомогательного метода, как это делает код в каждой из страниц, проще и удобнее для чтения. Он также упрощает для поддержания работы сайта, если вам нужно изменить вид заметки, можно изменять разметку в одном месте.

## <a name="creating-a-helper"></a>Создание вспомогательного метода

Эта процедура показано, как создать помощник, который создает Обратите внимание, как описано выше. Это простой пример, но пользовательских вспомогательных могут включать любые разметки и кода ASP.NET, которая может потребоваться.

1. В корневой папке веб-сайта, создайте папку с именем *приложения\_кода*. Это имя зарезервировано в ASP.NET, можно поместить код для компонентов, как вспомогательные методы.
2. В *приложения\_кода* создайте новую папку *.cshtml* файл и назовите его *MyHelpers.cshtml*.
3. Замените существующее содержимое следующим кодом:

    [!code-cshtml[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    Код использует `@helper` синтаксис для объявления нового вспомогательный класс с именем `MakeNote`. Этот вспомогательный определенного позволяет передавать параметр с именем `content` , может содержать сочетание текста и разметки. Вспомогательное приложение вставляет строку в текст заметки с помощью `@content` переменной.

    Обратите внимание, что этот файл имеет имя *MyHelpers.cshtml*, но вспомогательное приложение называется `MakeNote`. Можно поместить несколько пользовательских вспомогательных методов в один файл.
4. Сохраните и закройте файл.

## <a name="using-the-helper-in-a-page"></a>Использование вспомогательного метода на странице

1. В корневой папке создайте новый пустой файл с именем *TestHelper.cshtml*.
2. Добавьте в файл следующий код:

    [!code-html[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample2.html)]

    Чтобы вызвать вспомогательный объект был создан, используйте `@` следуют имя файла, где — вспомогательный объект точкой и имя поддержки. (Если имеется несколько папок *приложения\_кода* папки, можно использовать синтаксис `@FolderName.FileName.HelperName` для вызова вспомогательное приложение в любой папке уровень вложенности). Текст, добавляемый в кавычки в круглых скобках представляет собой текст, вспомогательное приложение будет отображаться как часть в примечании к веб-странице.
3. Сохраните страницу и запустите его в браузере. Вспомогательное приложение создает элемент Заметки справа где вызывается вспомогательный объект: между двумя абзацами.

    ![Снимок экрана со страницей в браузере, и как вспомогательный объект создаются разметку, которая помещает поле с указанным текстом.](creating-and-using-a-helper-in-an-aspnet-web-pages-site/_static/image1.jpg)

## <a name="additional-resources"></a>Дополнительные ресурсы


[Горизонтальная меню как вспомогательный Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341). Запись в блоге по Майк эти протоколы демонстрируется создание горизонтальной меню как вспомогательный объект с помощью разметки, CSS и кода.

[Использование HTML5 в ASP.NET Web Pages вспомогательные методы для WebMatrix и ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx). Вспомогательный класс, который выполняет визуализацию HTML5 показывает запись в блоге, что Sam `Canvas` элемента.

[Разница между @Helpers и @Functions в WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix). Описывает, Майк Бринд данной записи блога `@helper` синтаксис и `@function` синтаксис и когда следует использовать каждое.
