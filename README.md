# lab_8_llm_testing

## Версії середовища

| Компонент | Версія |
|-----------|--------|
| Docker | 29.9.3 (build 5457199) |
| Node.js | v24.16.0 |
| Git | 2.55.0.windows.5 |
| Ollama (образ) | ollama/ollama:0.40.16 |
| Модель | qwen3:0.6b |
| promptfoo | 0.121.17 |

## Як запустити

### Вимоги

- Docker (Docker Desktop на macOS/Windows або Docker Engine на Linux)
- Node.js 24 LTS (мінімум 22.22.0 або 20.20.0)
- Git

### 1. Клонувати репозиторій

```bash
git clone https://github.com/vlrkrv/lab_8_llm_testing.git
cd lab_8_llm_testing
```

### 2. Перевірити Node.js

```bash
node -v
```

Якщо версія нижча за 22.22.0 або 20.20.0 — встановіть Node 24 LTS через nvm:

```bash
nvm install 24
nvm use 24
node -v
```

У репозиторії є файл `.nvmrc` з текстом `24`, тому достатньо виконати `nvm use`.

### 3. Запустити Ollama у Docker (ізольовано)

```bash
docker run -d --name ollama -v ollama:/root/.ollama -p 127.0.0.1:11434:11434 --memory=2g --cpus=2 ollama/ollama:0.40.16
```

Порт прив'язано лише до `127.0.0.1` — модель недоступна з мережі. Перевірка:

```bash
docker ps
docker exec ollama ollama --version
```

У виводі `docker ps` має бути `127.0.0.1:11434->11434/tcp`.

### 4. Завантажити модель

```bash
docker exec -it ollama ollama pull qwen3:0.6b
docker exec ollama ollama list
```

Перевірка, що модель відповідає:

```bash
docker exec -it ollama ollama run qwen3:0.6b --think=false "Say hello"
```

### 5. Запустити тести promptfoo

```bash
npx promptfoo@0.121.17 eval
npx promptfoo@0.121.17 view
```

Перша команда виконує всі комбінації «промпт * тест-кейс», друга відкриває веб-інтерфейс з матрицею результатів.

Через недетермінованість моделі можна виконати повторні запуски:

```bash
npx promptfoo@0.121.17 eval --repeat 2
```

Попередження `ExperimentalWarning` і `telemetry.shutdown() timed out` — безпечні, на результат не впливають.

### 6. Очищення середовища (за потреби)

```bash
docker stop ollama
docker rm ollama
docker volume rm ollama
docker rmi ollama/ollama:0.40.16
```
