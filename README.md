# Docker + .NET Core = ♥️ 
Инструкция для освоения навыков запуска ASP.NET Core приложения в docker-контейнере

## Требования
Должны быть установлены
- [Docker](https://www.docker.com/community-edition#/download) (работает в Linux или на Windows 10, у процессора должна быть поддержка виртуализации)
- [.NET Core SDK](https://www.microsoft.com/net/download/core)


## Создание и запуск проекта на локальной машине

- Создайте и перейдите в папку "dotnetcore-on-docker-demo"
- Создание проекта:

```
> dotnet new web
```
- В файле Startup.cs замените строку 

````
> await context.Response.WriteAsync("Hello World!");
````
на 

````
> await context.Response.WriteAsync($"Hello World from {System.Runtime.InteropServices.RuntimeInformation.OSDescription}");
````

- Восстановите зависимости проекта

```
> dotnet restore
```
- Запустите проект

```
> dotnet run
```
> **Результат:** Проект должен открыться по умолчанию на 5000-м порту.
> Определившаяся система - Windows 10
- Остановите выполнение приложения `Ctrl+C`

## Запуск приложения в docker-контейнере на локальной машине

- Добавьте Dockerfile в корень проекта

````
# За основу берем образ dotnet версии 1.1.2 из публичного хаба 
FROM microsoft/dotnet:1.1.2-runtime
LABEL name "dotnetcore-on-docker-demo"

# Устанавливаем рабочую директорию
WORKDIR /app

# Копируем исполняемые файлы проекта в текущую папку (/app) контейнера 
COPY out .

# Устанавливаем переменную среды
ENV ASPNETCORE_URLS http://*:5000

# Открываем 5000-й порт
EXPOSE 5000

# Запуск приложения при старте контейнера
ENTRYPOINT ["dotnet", "dotnetcore-on-docker-demo.dll"]
````

- Собираем проект
```
> dotnet publish -c Release -o out
```
- Собираем образ с именем "dotnetcore-image"
```
> docker build -t dotnetcore-image .
```
- Поднимаем контейнер из нового образа
```
> docker run -p 5000:5000 -it --rm dotnetcore-image
```
-Проверяем 5000-й порт
> **Результат:** Проект должен открыться на 5000-м порту.
> Определившаяся система - Linux

## Деплой. Разворачивание приложения в docker-контейнере на удаленной машине 

- Скачать утилиту [now](https://zeit.co/download)
- Запустить now-win и передать путь к папке с проектом
```
> now-win .\..\dotnet-on-docker-demo\
```
> **Результат:** Для запуска утилиты без указания пути к папке с проектом нужно зарегистрировать утилиту в "Свойства системы" - "Переменные среды"
- Утилита запросит e-mail - нужно подтвердить регистрацию по ссылке из письма
- Далее утилита скопирует проект на сервер и начнет сборку образа и разворачивание контейнера
- В выводе консоли будет ссылка на развернутый проект
- Открываем ссылку
> **Результат:** Проект должен открыться.
> Определившаяся система - Linux
