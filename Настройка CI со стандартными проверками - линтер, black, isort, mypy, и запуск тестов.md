
**Что такое CI (Continuous Integration)?**  
CI — это процесс, при котором код автоматически собирается, тестируется и проверяется на ошибки при каждом коммите или запросе на слияние (pull request). В CI часто выполняются такие задачи, как запуск тестов, линтинговые проверки, форматирование кода и другие действия, чтобы обеспечить качество кода.

**Зачем это нужно?**

- **Автоматизация:** Программисты могут быть уверены, что их код прошел проверку и тесты без необходимости вручную проверять его.
- **Предотвращение ошибок:** Код проверяется и тестируется после каждого коммита, что предотвращает попадание багов в репозиторий.
- **Обеспечение качества:** Все проверки (например, линтеры, автоформатирование) проходят автоматически перед тем, как код попадет в основной репозиторий.

**Как настроить CI?** Для настройки CI можно использовать различные платформы, такие как **GitHub Actions**, **GitLab CI**, **Travis CI** и т. д. Рассмотрим настройку для **GitHub Actions**.

1. **Создать файл конфигурации GitHub Actions:** В корне проекта создайте папку `.github/workflows/` и внутри нее файл, например, `ci.yml`:
```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  lint:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: "3.12"

      - name: Install Poetry
        run: |
          curl -sSL https://install.python-poetry.org | python3 -
          echo "Poetry installed"

      - name: Install dependencies with Poetry
        run: |
          poetry install --no-root --no-interaction

      - name: Run pre-commit hooks
        run: |
          poetry run pre-commit run --all-files

  mypy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: "3.12"

      - name: Install Poetry
        run: |
          curl -sSL https://install.python-poetry.org | python3 -
          echo "Poetry installed"

      - name: Install dependencies with Poetry
        run: |
          poetry install --no-root --no-interaction

      - name: Run mypy
        run: |
          poetry run mypy .

  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: "3.12"

      - name: Install Poetry
        run: |
          curl -sSL https://install.python-poetry.org | python3 -
          echo "Poetry installed"

      - name: Install dependencies with Poetry
        run: |
          poetry install --no-root --no-interaction

      - name: Run tests
        run: |
          poetry run pytest tests/

```
   
2. **Описания шагов:**
    
    - **`checkout`** — извлекает код репозитория.
    - **`setup-python`** — устанавливает версию Python, которая используется в проекте.
    - **`install dependencies`** — устанавливает зависимости, включая `pre-commit`, `mypy`, и другие.
    - **`run pre-commit hooks`** — выполняет линтеры, форматирование и другие проверки с помощью `pre-commit`.
    - **`run mypy`** — запускает статический анализ типов с помощью `mypy`.
    - **`run tests`** — запускает тесты с использованием `pytest`.
3. **Настройка тестов:** В CI также можно настроить запуск тестов. Пример выше показывает использование `pytest` для запуска тестов из папки `tests/`.
    
4. **Что происходит?** При каждом коммите в ветку `main` или pull request:
    
    - Процесс CI будет запускать хуки для форматирования и проверки кода с помощью `black`, `isort`, `flake8`, `mypy`.
    - Также будет запускаться набор тестов с `pytest`.
    - Если проверки не проходят, CI завершится с ошибкой, и коммит не будет принят.
