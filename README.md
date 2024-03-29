# Итоговое задание по дисциплине "Программная инженерия"

Этот файл является отчетом, в нем описаны цели проекта, используемые подходы и технологии.
[Презентация](https://docs.google.com/presentation/d/1yU2u_vwVCvVOnOgUh-Q1wnvth5qnvQP0YyoCf-sPF74/edit?usp=sharing)

Цель семестрового проекта – закрепить знания по контейнеризации приложений, непрерывной интеграции и метрикам.

## Go-приложение go-chartographer💎

Проект является тестовым заданием на стажировку. Целью создания были практика нового языка и устройство на стажировку. Задачи были следующие:
- написать сервис по работе с изображениями формата BMP, предоставляющий HTTP API
- покрыть проект модульными тестами

[Постановка задачи сервиса (документация API)](https://github.com/Dimedrolity/go-chartographer/blob/master/README_task.md)

[Описание решения](https://github.com/Dimedrolity/go-chartographer/blob/master/README.md) 

Подключен в текущий репозиторий как *git submodule*, так как является самостоятельным проектом, не зависит от текущего репозитория с инфраструктурой.

Дополнительно:

### Зависимости
Используется подход *вендоринг* - зависимости хранятся в репозитории (в папке vendor).

### .run
Используется для конфигурации запуска тестов и приложения с помощью IDE Goland. При наличии xml конфигов, IDE добавляет конфигурации автоматически
![./screenshots/.run.png](./screenshots/.run.png)

### Makefile
Используется для фиксации команд для запуска тестов, сборки и запуска.

### Docker🐳
Используется для контейнеризации Go-приложения.

#### multi-stage build
Собирается в контейнере *golang:1.17-alpine*, исполняемый файл перекладывается в контейнер *alpine*. Таким образом вес итогового образа **13 Mb**.

#### docker-compose
Используется для:
- конфигурации порта (чтобы не писать вручную при запуске через `docker run`)
- связанных директорий (*volumes* a.k.a. *bind mount*)
- установки переменных окружения в контейнере (по умолчанию используется файл .env)

Используется версия 2 файла docker-compose.yml, так как она позволяет указать ограничение по оперативной памяти контейнера (по условию тестового задания проверка сервиса будет в контейнере с 2 Гб памяти).
Версия 3 не позволяет указать ограничение, так как она создана для работы в связке с *docker swarm* - [ответ от авторов](https://github.com/docker/compose/issues/4513)

### Github Actions 🤖

Используется для реализации подхода Continuous Integration. 

При пуше на Github на билд сервере происходит:

- запуск авто-тестов
- сборка под Windows и Linux
- создается Release на Github с *git тегом* latest
- в раздел assets релиза добавляются файлы сборки

![release](./screenshots/release.png)



## СAdvisor🦉

Используется для сбора метрик с Docker-контейнера Go-приложения.



## AlertManager⚠️

Используется для оповещений о памяти на Gmail.



## Prometheus🔥

Используется для получения метрик с cAdvisor.

Важно отметить, что контейнеры запускаются в одной сети благодаря docker-compose, поэтому можно обращаться к контейнеру не по ip, а по названию сервиса в docker-compose.yml. Так делается в prometheus.yml для cAdvisor и AlertManager. 

# Запуск

Должны быть установлены Git, Docker и docker-compose.

```bash
git clone --recurse-submodules git@github.com:Dimedrolity/devops-final.git

cd devops-final

docker-compose up --build
```

# Результат

С помощью docker-compose создаются docker образы и запускаются контейнеры. 

На **8080** порту будет **REST API Go-приложения**.
![./screenshots/go_api.png](./screenshots/go_api.png)

На **8081** порту **cAdvisor** Web UI с метриками контейнера Go-приложения.
![./screenshots/c_advisor.png](./screenshots/c_advisor.png)

На **9090** порту **Prometheus** Web UI с метриками контейнера Go-приложения
![./screenshots/prometheus.png](./screenshots/prometheus.png)

При отправке запроса в Go-приложение расход памяти увеличивается до 20+ Mb, и приходит оповещение на почту ![./screenshots/gmail.png](./screenshots/gmail.png)

# Проблема 🤔

Не работает метрика container_memory_usage_bytes{name="devops-final_go-chartographer_1"}. Сначала подумал, что это из-за WSL (так как создаваемые сервисом изображения лежат на диске основной системы и связаны с контейнером, а внутри WSL их нет). Однако, при запуске в виртуалке тоже не работает.

Показывает килобайты данных, хотя контейнер должен весит как минимум 20 мегабайт.![./screenshots/fs_metric_fail.png](./screenshots/fs_metric_fail.png)

В cAdvisor показывается только /dev/sda5![./screenshots/cadvisor_fs.png](./screenshots/cadvisor_fs.png)

Хотя в конфиге docker-compose.yml связывается вся директория /dev

```
    volumes:
      ...
      - /dev:/dev:ro
```

# Итоги

За семестр были изучены и применены следующие технологии:

- Контейнеризация (Docker и Docker compose)
- Continuous Integration (Github Actions)
- Метрики и оповещений (Prometheus, Alertmanager, cAdvisor) 

Также за семестр были изучены Grafana и Ansible, но не применены в итоговом задании.

В итоге я освоил новые инструменты, закрепил и систематизировал имеющиеся знания.