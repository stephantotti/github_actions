# 🗂️ Estrutura de Diretórios e Arquivos

## 📘 Introdução

Criar workflows no GitHub Actions começa pela compreensão da estrutura organizacional utilizada pela plataforma.

A organização correta dos diretórios e arquivos é essencial para que o GitHub reconheça automaticamente os workflows e execute as automações configuradas.

A estrutura é simples, porém extremamente importante para manter pipelines organizados, escaláveis e fáceis de manter.

---

# 🧩 Definição

O GitHub Actions utiliza uma estrutura padronizada onde todos os workflows devem estar localizados no diretório:

```bash
.github/workflows/
```

Cada arquivo YAML presente nesse diretório representa um workflow independente.

O GitHub detecta automaticamente esses arquivos e os disponibiliza na aba **Actions** do repositório.

---

# 📁 Estrutura de Diretórios

## 📌 Estrutura padrão

```text
repositorio/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── deploy.yml
│       └── release.yml
├── src/
├── tests/
└── README.md
```

---

## 📄 Papel de cada arquivo

| Arquivo | Finalidade |
|---|---|
| 🧪 `ci.yml` | Integração contínua |
| 🚀 `deploy.yml` | Deploy automatizado |
| 📦 `release.yml` | Automação de releases |

---

# ⚙️ Funcionamento Automático

O GitHub Actions monitora automaticamente:

```bash
.github/workflows/
```

Todos os arquivos:

```bash
.yml
```

ou

```bash
.yaml
```

são tratados como workflows válidos.

✅ Não é necessário:

- registro manual
- configuração extra
- habilitação individual

---

# 🏷️ Convenções de Nomenclatura

## 📌 Extensões aceitas

- `.yml`
- `.yaml`

A extensão `.yml` é mais comum por ser mais curta.

---

## ✅ Exemplos recomendados

```text
ci.yml
deploy-production.yml
test-coverage.yml
security-scan.yml
release-automation.yml
```

---

## 📏 Boas práticas

- 🔤 Usar kebab-case
- 🧠 Nome descritivo
- 🚫 Evitar espaços
- 🚫 Evitar caracteres especiais
- 🏷️ Prefixar ambientes quando necessário

---

# ⚙️ Sintaxe YAML Básica

# 📄 Estrutura Fundamental

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main ]

  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'corretto'

      - name: Run tests
        run: ./mvnw test
```

---

# 🧱 Elementos Obrigatórios

## 🏷️ 1. name

Nome exibido na interface do GitHub.

```yaml
name: "Spring Boot CI/CD"
```

Embora opcional, é altamente recomendado.

---

## 🚀 2. on

Define quais eventos disparam o workflow.

```yaml
on:
  push:

  pull_request:

  schedule:
    - cron: '0 2 * * 1'

  workflow_dispatch:
```

---

## 📌 Eventos comuns

| Evento | Descrição |
|---|---|
| 🚀 `push` | Push em branches |
| 🔀 `pull_request` | Pull requests |
| ⏰ `schedule` | Execução agendada |
| 🖱️ `workflow_dispatch` | Execução manual |

---

## 🧩 3. jobs

Define os jobs executados pelo workflow.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest

  build:
    runs-on: ubuntu-latest
```

---

# ⚡ Exemplo com Dependência entre Jobs

```yaml
jobs:
  test:
    name: Unit Tests
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: npm test

  build:
    name: Build Application

    needs: test

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: npm run build
```

---

## 🔗 O que faz o `needs`

```yaml
needs: test
```

Define que o job `build` só executará após o job `test` finalizar com sucesso.

---

# 📏 Sintaxe YAML Essencial

## 📌 Indentação

Use sempre:

- ✅ 2 espaços
- ❌ Nunca tabs

---

## ✅ Correto

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

---

# 📋 Listas

## ✅ Formato inline

```yaml
branches: [ main, develop ]
```

---

## ✅ Formato tradicional

```yaml
branches:
  - main
  - develop
```

---

# 📝 Strings

Aspas são opcionais, mas recomendadas.

```yaml
name: "My Workflow"

run: echo "Hello World"
```

---

# 📄 Multiline Commands

Utilize `|` para múltiplas linhas.

```yaml
run: |
  echo "Linha 1"
  echo "Linha 2"
  ./script.sh
```

---

# 🌎 Variáveis e Contextos

## 📌 Variáveis globais

```yaml
env:
  NODE_VERSION: '18'
```

---

## 📌 Uso em steps

```yaml
- name: Use environment variable
  run: echo "Node version: ${{ env.NODE_VERSION }}"
```

---

# 🧠 Contextos do GitHub

O GitHub fornece diversos contextos automáticos.

```yaml
- name: Access GitHub context
  run: |
    echo "Repository: ${{ github.repository }}"
    echo "Actor: ${{ github.actor }}"
    echo "SHA: ${{ github.sha }}"
```

---

## 📌 Contextos comuns

| Contexto | Descrição |
|---|---|
| 📦 `github.repository` | Nome do repositório |
| 👤 `github.actor` | Usuário que disparou |
| 🔐 `github.sha` | Commit SHA |
| 🌿 `github.ref` | Branch ou tag |

---

# 🎯 Output Esperado

Após criar o workflow corretamente:

## ✅ O que acontece

- 👀 Workflow aparece automaticamente na aba Actions
- 🚀 Execução ocorre em push/pull request
- 📄 Logs detalhados são exibidos
- ⚡ Cache pode acelerar builds
- 📊 Relatórios ficam integrados à interface

---

# 🧪 Exemplo Visual do Fluxo

```text
Push no repositório
        ↓
GitHub detecta workflow
        ↓
Runner é provisionado
        ↓
Jobs são executados
        ↓
Steps processados
        ↓
Logs e resultados exibidos
```

---

# 📌 Boas Práticas

## ✅ Recomendações importantes

- 📁 Manter workflows organizados
- 🏷️ Usar nomes claros
- 📏 Padronizar indentação
- 🧩 Separar workflows por responsabilidade
- 🔄 Evitar arquivos gigantes
- 🧠 Documentar workflows complexos

---

# 🏁 Conclusão

A estrutura de diretórios do GitHub Actions é simples, porém extremamente poderosa.

O diretório:

```bash
.github/workflows/
```

centraliza toda automação do projeto, permitindo organização clara e execução automática dos pipelines.

Dominar:

- 📁 Estrutura de diretórios
- ⚙️ Sintaxe YAML
- 🚀 Eventos
- 🧩 Jobs
- 📄 Steps
- 🌎 Variáveis e contextos

é o primeiro passo para construir workflows robustos, reutilizáveis e escaláveis.