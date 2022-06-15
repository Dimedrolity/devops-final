# Итоговое задание по дисциплине Программная инженерия

## Go-приложение go-chartographer💎

Является тестовым заданием на стажировку. [Постановка задачи сервиса (документация API)](./go-chartographer/README_task.md) 

[Описание решения](./go-chartographer/README.md) 

Подключен в текущий репозиторий как *git submodule*.

## Docker🐳
Используется для контейнеризации Go-приложения.

## СAdvisor🦉
Используется для сбора метрик с Docker-контейнера для Go-приложением.

## Prometheus🔥
Используется для получения метрик с cAdvisor.

## AlertManager
TODO

## Запуск
Должны быть установлены Git, Docker и docker-compose.

```
git clone --recurse-submodules git@github.com:Dimedrolity/devops-final.git

cd devops-final

docker-compose up --build
```

# Результат

На **8080** порту будет REST API Go-приложения.
![./screenshots/go_api.png](./screenshots/go_api.png)

На **8081** порту cAdvisor Web UI с метриками контейнера Go-приложения.
![./screenshots/c_advisor.png](./screenshots/c_advisor.png)

На **9090** порту Prometheus Web UI с метриками контейнера Go-приложения
![./screenshots/prometheus.png](./screenshots/prometheus.png)