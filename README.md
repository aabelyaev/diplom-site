
1. build Запускается при любом коммите в любую ветку. Выполняется werf cr для аутентификации в Container Registry и werf build для сборки docker image и отправки в registry. Тег образа формируется исходя из содержимого слоев.

2. Деплой запускается при создании тега с семантическим версионированием (в виде строки "v1.2.3"). В процессе деплоя выполняются следующие действия:

- Сборка образа: werf cr собирает образ на основе манифестов в k8s.
- Публикация образа в registry: После сборки образа, он публикуется в registry.
- Деплой в кластер k8s: После публикации образа, он деплоится в k8s-кластер на основе манифестов.
Всё это происходит после запуска werf converge, который является ключевым шагом в процессе деплоя с помощью Werf.

 
Примерный вывод параметров деплоя
```
name: k8s
run-name: Deploy "main" 🚀
on:
  push:
    tags:
      - 'v1.2.3'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Werf init
        uses: werf/actions/install@v2

      - name: Werf deploy
        run: |
          source "$(werf ci-env github --as-file)"
          werf cr
          werf converge
        env:
          WERF_KUBECONFIG_BASE64: "AQIDBA=="
          WERF_REPO: "https://github.com/aabelyaev/repo.git"
          WERF_PASSWORD: "secret_password"
          WERF_USERNAME: "username"
          WERF_ENV: prod
          WERF_ADD_CUSTOM_TAG_1: "main"
```

Параметры деплоя:
- Время деплоя: 12:00
- Имя деплоя: Deploy "main" 
- Тег версии: v1.2.3
- Хост: ubuntu-latest
- Пользователь: username
- Пароль: secret_password
- Контекст выполнения: prod
- Содержимое тега 1: main