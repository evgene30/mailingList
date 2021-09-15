# Клиент почтовой рассылки smtpServer v. 0.1.0 beta

## Основные приимущества:

-   Осуществление рассылки посредством любого привязанного адреса электронной почты;
-   Отправка писем на любое количество адресов (массив адресов);
-   Всегда один адрес в строке отправления;
-   Прикрепление к письму любых файлов (кроме файлов кода);
-   Реализация разделения входящего массива адресов, возможность остановиться и продолжить места остановки (разделение на отправленные и необработанные данные);
-   Возможность редактировать тело письма посредством вставки и стилизации html кода;
-   Автоматическая обработка пустых значений адресов в массиве данных;

## Описание работы:

1. В папаке `./data` находится файл `./data/company.json`. Это основной сведенный и обработанный файл с данными компаний. <b>Перед началом работы обязательно делаем его копию в корневую директорию!</b>

<br>
<b>2. Основной файл управления рассылкой `./smtpServer.js`. </b>

Ищем "блок авторизации пользователя" и заполняем данные вашей почты:

```JavaScript
// блок авторизации пользователя
    const transporter = nodemailer.createTransport({
        service: "gmail", // тип почты (если gmail)
        auth: {
            user: "proverka@mail.com", // адрес почты
            pass: "proverka12345", // пароль от почты
        },
    });
```

<b>3. Настраиваем блок отправки сообщения:</b>

```JavaScript
// блок отправки сообщений
    transporter.sendMail(
        {
            from: "Name Lastname <proverka@mail.com>", // от кого письмо, подпись отправителя
            to: `${item.email}`, // адрес куда отправляем
            subject: "Job seeker Junior Frontend Web Developer ", // тема письма (название)
            html: bodyHtml(item.name), // текст письма, можно передать как в виде простого текста так форматированного из html тегов (и стилей)
            attachments: [
                {
                    filename: "My_File.docx", // имя передаваемого файла
                    path: "C:/Users/user/Downloads/Send/My_File.docx", // путь к файлу (относительный либо абсолютный)
                },
            ],
        },
```

<b>4. Настраивам шаблон HTML вашего письма. (При желании можно не подключать)</b>

Точка подключения. В нутрь функции передаем название компании для автоматического ее отображения в нашем тексте письма

```JavaScript
html: bodyHtml(item.name), // текст письма
```

Если не подключаем можем заполнить обычным текстом:

```JavaScript
html: "Hello World", // текст письма
```

Шаблон письма загружается из файла `./htmlBody.js`.

Верстка шаблона осуществляется табличным способом, полностью поддерживается не всеми почтовыми клиентами.

Посмотреть, как выглядят ваше письмо в разных почтовых клиентах, можно в сервисах ,litmus.com и emailonacid.com. Они тестируют письма:
на любых разрешениях экрана;
в различных почтовых клиентах, десктопных и мобильных приложениях;
на разных версиях одной программы.

<b>5. Указываем какое количество писем которое мы хотим отправить.</b>

По умолчанию переменная numberMailSend равна 0. Это значит что отправка писем будет осуществлена по всей длине массива.

```JavaScript
let numberMailSend = 0; // указываем сколько писем хотим отправить. По умолчанию - если 0  - значит все по длине массива.
```

Если вы хотите отправить 20 писем, меняем значение переменной:

```JavaScript
let numberMailSend = 20; // указываем сколько писем хотим отправить. По умолчанию - если 0  - значит все по длине массива.
```

<b>5. После настройки.</b>

После настройки и запуска вашей программы алгоритм работы будет следующим:

1. Программа начинает работать со скопированным в корень файлом `./company.json` и последовательно отправлять письма.

2. Программа автоматически создаст файл `./sendFirm.json` в корне проекта где будут размещены данные о компаниях в которые писама были отправлены.

3. После отправки указанного вами количества писем (например 20), программа автоматически удалит и перезапишет из начального файла `./company.json` те объекты, на адреса на которых была успешная отправка и соответсвенно выведет в консоль данные:

-   о общем количестве отправленных сообщений (в файле sendFirm);
-   о пустых адресах (пустые адреса просто удаляеются без перезаписи);
-   о количестве отправленных писем за текущую сессиюж
-   о оставшихся (в файле company.json) не обработанных письмах.
