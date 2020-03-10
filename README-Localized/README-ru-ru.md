---
page_type: sample
products:
- office-outlook
- office-365
languages:
- javascript
extensions:
  contentType: samples
  technologies:
  - Add-ins
  createdDate: 6/26/2015 3:02:27 PM
---
# Art Curator для Angular 

В этом примере показано, как извлекать сообщения и вложения из Office 365 с помощью API Почты Outlook. Он создан для [iOS](https://github.com/OfficeDev/O365-iOS-ArtCurator), [Android](https://github.com/OfficeDev/O365-Android-ArtCurator), браузера (веб-приложение Angular) и [Windows Phone](https://github.com/OfficeDev/O365-WinPhone-ArtCurator). Ознакомьтесь с нашей [статьей на носителе](https://medium.com/office-app-development) и посмотрите это [краткое руководство на YouTube](https://www.youtube.com/watch?v=M88A6VB9IIw&feature=youtu.be).

Art Curator — это новый подход к просмотру папки "Входящие". Представьте, что вы владеете компанией, которая продает дизайнерские футболки. Как владелец компании вы получаете много сообщений с рисунками от художников. Вместо того чтобы с помощью Outlook открывать каждое отдельное сообщение, скачивать вложенный рисунок и открывать его для просмотра, можно использовать Art Curator для просмотра вложений (только файлы JPG и PNG) из папки "Входящие". Так намного удобнее выбирать понравившиеся рисунки.

[![Снимок экрана: Art Curator](./README Assets/AC_Angular.png)](https://youtu.be/4LOvkweDfhY "Щелкните, чтобы просмотреть пример в действии.")

В этом примере показаны следующие операции из**API Почты Outlook**:
* [Получить папки](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetFolders)
* [Получить сообщения](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Getmessages) (в том числе фильтрация и использование SELECT)
* [Получить вложения](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#GetAttachments)
* [Обновить сообщения](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Updatemessages)
* [Создать и отправить сообщения](https://msdn.microsoft.com/office/office365/APi/mail-rest-operations#Sendmessages) (с вложениями или без них) 

Кроме того, в этом примере показана проверка подлинности в Azure Active Directory с использованием [Библиотеки проверки подлинности Active Directory (ADAL) для JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

<a name="prerequisites"></a>
## Необходимые компоненты

Для этого примера требуются приведенные ниже компоненты:
* [Node.js](https://nodejs.org/). Платформа Node необходима для установки зависимостей и запуска примера на сервере разработки.
* Учетная запись Office 365. Вы можете подписаться на [план Office 365 для разработчиков](http://aka.ms/ro9c62), включающий ресурсы, которые необходимы для создания приложений Office 365.

<a name="configure"></a>
## Регистрация приложения

1. Чтобы быстро приступить к работе с этим примером, перейдите к [Средству регистрации приложений на портале для разработчиков Outlook](https://dev.outlook.com/appregistration).
2. На **шаге 1** выполните вход с помощью существующей учетной записи Office 365 или нажмите кнопку, чтобы получить бесплатную пробную версию. После входа перейдите к следующему действию.
3. На **шаге 2** заполните форму, используя приведенные ниже значения.
	* *Имя приложения:* Art Curator
	* *Тип приложения:* Одностраничное приложение (SPA)
	* *URI перенаправления:* http://127.0.0.1:8080/
	* *URL-адрес домашней страницы:* http://artcurator.{your_subdomain}.com (поддомен .onmicrosoft вашего клиента Office 365)
4. На **шаге 3** выберите следующие разрешения в разделе **API почты**.
	* *Чтение и запись почты*
	* *Отправка почты*
5. На **шаге 4** нажмите кнопку **Зарегистрировать приложение**, чтобы зарегистрировать приложение в Azure Active Directory.
6. Наконец, скопируйте **идентификатор клиента** из формы для использования в следующем разделе.

<a name="run"></a>
## Запуск приложения

Откройте файл *app/scripts/app.js* и замените *{your_tenant}* на поддомен .onmicrosoft, который вы указали для своего клиента Office 365, и идентификатор клиента для приложения, зарегистрированного в Azure, который вы получили в Средстве регистрации приложений на портале для разработчиков на предыдущем шаге, в строках 46 и 47 соответственно. 

Затем установите необходимые зависимости и запустите проект с помощью командной строки. Для начала откройте командную строку и перейдите в корневую папку. Затем выполните указанные ниже действия.

1. Установите зависимости проекта с помощью команды ```npm install```.
2. Теперь, когда все зависимости проекта установлены, запустите сервер разработки с помощью файла ```node server.js``` в корневой папке.
3. Введите адрес ```http://127.0.0.1:8080/``` в веб-браузере.

<a name="understand"></a>
## Разбор кода

### Подключение к Office 365

Для запроса и получения маркеров при использовании API Office 365 необходима проверка подлинности. В этом проекте она выполняется в [Azure Active Directory с помощью Библиотеки Azure Active Directory (ADAL) для JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Служба настроена в файле *app/app.js* в функции конфигурации модуля. В файле *app/controllers/navBarController.js* есть две функции, которые отвечают за вход в службу Azure Active Directory и выход из нее. Azure Active Directory также отвечает за получение маркеров. 

### API почты

В этом проекте используются стандартные вызовы REST для связи с API почты. Дополнительные сведения о доступных конечных точках можно найти в [справочнике по REST API Почты Outlook](https://msdn.microsoft.com/en-us/office/office365/api/mail-rest-operations).

Все функции API почты включены в контроллер *app/controllers/mainController.js*. Сначала он извлекает сведения обо всех папках, доступных на клиенте пользователя, и использует значение в строке *localStorage* для поиска целевой папки. После этого он извлекает сведения о 50 последних непрочитанных сообщениях с вложениями. Затем с помощью вызовов он извлекает содержимое каждого из этих вложений. На этом этапе все сообщения и содержимое вложений извлечены и хранятся в массиве, доступном для просмотра.

Кроме того, контроллер *mainController.js* может помечать сообщения как прочтенные, а также создавать и отправлять ответы. 

### Ограничения

Указанные ниже функции не включены в текущую версию.

* Поддержка файлов, отличных от PNG и JPG
* Обработка одного сообщения электронной почты с несколькими вложениями
* Разбиение на страницы (получение более 50 сообщений)
* Обработка уникальности имени папки
* Папка для отправки должна быть папкой верхнего уровня

## Уведомление о безопасности
[ADAL JS](https://github.com/AzureAD/azure-activedirectory-library-for-js) не проверяет маркер, полученный от Azure AD. За это отвечает серверная часть приложения, и до вызова серверной части неизвестно, получил ли пользователь допустимый маркер. Из соображений безопасности в бизнес-приложения должен быть встроен серверный компонент для проверки подлинности пользователей. Без этой проверки маркеров ваше приложение уязвимо для атак, например [атак по принципу подозрительного доверия](https://en.wikipedia.org/wiki/Confused_deputy_problem). Дополнительные сведения см. в этой [записи блога](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/).

<a name="questions-and-comments"></a>
## Вопросы и комментарии

- Если у вас возникли проблемы с запуском этого образца, пожалуйста [зарегистрируйте неполадку](https://github.com/OfficeDev/O365-Angular-ArtCurator/issues).
- Общие вопросы про API Office 365, публикуйте в[Stack Overflow](http://stackoverflow.com/). Убедитесь в том, что ваши вопросы или комментарии помечены тегом \[Office365].
  
<a name="additional-resources"></a>
## Дополнительные ресурсы

* [Создание приложения Angular с помощью API Office 365](http://aka.ms/get-started-with-js)
* [Общие сведения о платформе API Office 365](http://msdn.microsoft.com/office/office365/howto/platform-development-overview)
* [Центр разработчиков Office](http://dev.office.com/)
* [Art Curator для iOS](https://github.com/OfficeDev/O365-iOS-ArtCurator)
* [Art Curator для Android](https://github.com/OfficeDev/O365-Android-ArtCurator)
* [Art Curator для Windows Phone](https://github.com/OfficeDev/O365-WinPhone-ArtCurator)

## Авторские права
(c) Корпорация Майкрософт (Microsoft Corporation), 2015. Все права защищены.


Этот проект соответствует [Правилам поведения разработчиков открытого кода Майкрософт](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в разделе [часто задаваемых вопросов о правилах поведения](https://opensource.microsoft.com/codeofconduct/faq/). Если у вас возникли вопросы или замечания, напишите нам по адресу [opencode@microsoft.com](mailto:opencode@microsoft.com).
