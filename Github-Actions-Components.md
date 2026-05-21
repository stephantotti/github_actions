# 🚀 Componentes do GitHub Actions

## 📘 Introdução

Para dominar o GitHub Actions, é essencial compreender seus componentes fundamentais. Cada elemento trabalha em conjunto para criar um sistema robusto de automação. Essa estrutura modular permite construir desde verificações simples até pipelines complexos de integração contínua e deploy contínuo (CI/CD).

---

## 🧩 Definição

Os componentes do GitHub Actions formam uma arquitetura hierárquica onde cada elemento possui responsabilidades específicas:

* ⚙️ **Workflows:** orquestram todo o processo
* 📡 **Events:** definem quando executar
* 🧱 **Jobs:** organizam o trabalho
* 🔹 **Steps:** executam tarefas individuais
* ♻️ **Actions:** reutilizam funcionalidades prontas
* 🖥️ **Runners:** fornecem o ambiente de execução

---

# ⚙️ 1. Workflows

Os workflows são processos automatizados definidos em arquivos YAML localizados na pasta:

```bash
.github/workflows/
```

### 📄 Exemplo

```yaml
name: Complete CI/CD Pipeline

on:
  push:
    branches: [ main ]

  schedule:
    - cron: '0 2 * * 1'

env:
  JAVA_VERSION: '17'

jobs:
  test:
    # Job de teste

  build:
    # Job de build

  deploy:
    # Job de deploy
```

### ✅ Neste exemplo

* O workflow executa em pushes na branch `main`
* Também roda automaticamente toda segunda-feira às 2h
* Define uma variável global `JAVA_VERSION`
* Possui três jobs principais: teste, build e deploy

---

# 📡 2. Events

Os eventos são responsáveis por disparar workflows automaticamente.

### 📄 Exemplo

```yaml
on:
  push:
    branches: [ main, develop ]
    paths: [ 'src/**', 'pom.xml' ]

  pull_request:
    branches: [ main ]
    types: [ opened, synchronize, reopened ]

  schedule:
    - cron: '30 5 * * 1-5'

  workflow_dispatch:
    inputs:
      environment:
        description: 'Ambiente de deploy'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

  release:
    types: [ published ]
```

## 📌 Principais tipos de eventos

| Evento              | Descrição                              |
| ------------------- | -------------------------------------- |
| `push`              | Executa quando há push no repositório  |
| `pull_request`      | Executa em eventos relacionados a PRs  |
| `schedule`          | Executa em horários programados        |
| `workflow_dispatch` | Permite execução manual                |
| `release`           | Executa quando uma release é publicada |

---

# 🧱 3. Jobs

Jobs representam conjuntos de tarefas executadas dentro do mesmo runner.

### 📄 Exemplo

```yaml
jobs:
  test:
    name: 'Testes Unitários'
    runs-on: ubuntu-latest

    strategy:
      matrix:
        java-version: [ 11, 17, 21 ]

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: ${{ matrix.java-version }}
          distribution: 'corretto'

  build:
    name: 'Build da Aplicação'
    needs: test
    runs-on: ubuntu-latest

    if: github.ref == 'refs/heads/main'

    outputs:
      artifact-name: ${{ steps.build.outputs.artifact }}

    steps:
      - name: Build
        id: build
        run: |
          ./mvnw clean package
          echo "artifact=devops-api-$(date +%Y%m%d).jar" >> $GITHUB_OUTPUT
```

## 📌 Recursos importantes dos jobs

* 🔗 `needs`: define dependências entre jobs
* 🧪 `strategy.matrix`: executa o mesmo job em múltiplos cenários
* ✅ `if`: executa condicionalmente
* 📤 `outputs`: compartilha informações entre jobs

---

# 🔹 4. Steps

Steps são ações individuais executadas sequencialmente dentro de um job.

### 📄 Exemplo

```yaml
steps:
  - name: Checkout Repository
    uses: actions/checkout@v4
    with:
      fetch-depth: 0

  - name: Cache Dependencies
    uses: actions/cache@v4
    with:
      path: ~/.m2
      key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}

  - name: Run Custom Script
    run: |
      echo "Iniciando build..."
      chmod +x ./scripts/build.sh
      ./scripts/build.sh
      echo "Build concluído!"

  - name: Deploy to Production
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    run: echo "Deploying to production..."
```

## 📌 Tipos de steps

| Tipo   | Descrição                 |
| ------ | ------------------------- |
| `uses` | Utiliza uma action pronta |
| `run`  | Executa comandos shell    |
| `if`   | Executa condicionalmente  |

---

# ♻️ 5. Actions

Actions são componentes reutilizáveis que automatizam tarefas específicas.

## 🌎 Action do Marketplace

```yaml
- name: Deploy to AWS
  uses: aws-actions/configure-aws-credentials@v4
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: us-east-1
```

## 🏠 Action local customizada

```yaml
- name: Custom Notification
  uses: ./.github/actions/notify-teams
  with:
    webhook-url: ${{ secrets.TEAMS_WEBHOOK }}
    message: 'Deploy concluído com sucesso!'
```

## 🧩 Action composta

```yaml
- name: Setup Build Environment
  uses: ./.github/actions/setup-build
  with:
    java-version: '17'
    node-version: '18'
```

## 📌 Benefícios das actions

* ♻️ Reutilização de código
* 📏 Padronização de processos
* 🧠 Redução de complexidade
* 🔌 Integração com serviços externos

---

# 🖥️ 6. Runners

Runners são máquinas responsáveis pela execução dos workflows.

### 📄 Exemplo

```yaml
jobs:
  ubuntu-job:
    runs-on: ubuntu-latest

  windows-job:
    runs-on: windows-latest

  macos-job:
    runs-on: macos-latest

  custom-job:
    runs-on: [ self-hosted, linux, x64 ]

  container-job:
    runs-on: ubuntu-latest

    container:
      image: amazoncorretto:17
      options: --user root
```

## 📌 Tipos de runners

| Runner        | Descrição                             |
| ------------- | ------------------------------------- |
| GitHub-hosted | Infraestrutura gerenciada pelo GitHub |
| Self-hosted   | Infraestrutura própria                |
| Containerized | Execução dentro de containers         |

---

# 🧪 Exemplo Prático Completo

A seguir, um workflow completo integrando todos os componentes do GitHub Actions:

```yaml
name: Spring Boot CI/CD Complete

on:
  push:
    branches: [ main, develop ]

  pull_request:
    branches: [ main ]

  workflow_dispatch:

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    name: 'Testes e Qualidade'
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_PASSWORD: postgres

        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'corretto'

      - name: Cache Maven
        uses: actions/cache@v4
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}

      - name: Run Tests
        run: ./mvnw test
        env:
          SPRING_PROFILES_ACTIVE: test

      - name: Generate Coverage Report
        run: ./mvnw jacoco:report

      - name: Upload Coverage
        uses: codecov/codecov-action@v4
        with:
          file: ./target/site/jacoco/jacoco.xml

  build:
    name: 'Build e Docker'
    needs: test
    runs-on: ubuntu-latest

    if: github.ref == 'refs/heads/main'

    outputs:
      image-digest: ${{ steps.build.outputs.digest }}

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'corretto'

      - name: Build Application
        run: ./mvnw clean package -DskipTests

      - name: Log into Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and Push Docker
        id: build
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest

  deploy:
    name: 'Deploy Staging'
    needs: build
    runs-on: ubuntu-latest
    environment: staging

    steps:
      - name: Deploy to Staging
        run: |
          echo "Deploying image: ${{ needs.build.outputs.image-digest }}"
          echo "Deploy simulado para staging"
```

---

# 🎯 Resultado Esperado

A execução do workflow acima produzirá:

* ✅ Execução automática de testes
* 📊 Verificação de qualidade e cobertura
* 🏗️ Build da aplicação
* 🐳 Criação e publicação de imagem Docker
* 🚀 Deploy automatizado para staging
* 📦 Disponibilização de artifacts e logs
* 📈 Feedback visual detalhado no GitHub

---

# 🏁 Conclusão

Os componentes do GitHub Actions trabalham em conjunto para criar um ecossistema completo de automação.

* ⚙️ **Workflows** orquestram os processos
* 📡 **Events** disparam execuções
* 🧱 **Jobs** organizam o fluxo de trabalho
* 🔹 **Steps** executam tarefas
* ♻️ **Actions** promovem reutilização
* 🖥️ **Runners** fornecem o ambiente de execução

Essa arquitetura modular oferece grande flexibilidade, permitindo desde automações simples até pipelines corporativos complexos.

Dominar cada componente é essencial para construir soluções robustas, eficientes e maintíveis, aproveitando todo o potencial do GitHub Actions.
