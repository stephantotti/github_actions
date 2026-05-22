# ⚙️ Workflow Básico no GitHub Actions

## 📘 Introdução

Um workflow básico é o alicerce de qualquer automação no GitHub Actions.

Compreender sua estrutura fundamental é essencial para criar pipelines:

- 🚀 Eficientes
- 🧠 Organizados
- 🔄 Automatizados
- 📦 Escaláveis

Cada workflow representa um processo automatizado executado sob determinadas condições definidas pelo desenvolvedor.

---

# 🧩 Definição

Um workflow básico no GitHub Actions é um arquivo YAML que define um processo automatizado contendo três elementos fundamentais:

- 🏷️ `name` → identificação do workflow
- 🚀 `on` → eventos de trigger
- ⚙️ `jobs` → tarefas executadas

Esses elementos formam a base de qualquer automação no GitHub.

---

# 🧱 Estrutura Fundamental

## 📄 Exemplo mínimo

```yaml
name: Workflow Básico

on:
  push:
    branches: [ main ]

jobs:
  exemplo:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: echo "Workflow executado com sucesso!"
```

---

# 🏷️ 1. Elemento `name`

O campo `name` define o nome exibido na interface do GitHub Actions.

---

## 📄 Exemplo

```yaml
name: "Meu Primeiro Workflow"
```

---

## 📌 Função

Permite identificar facilmente o workflow na aba:

```text
Actions
```

do GitHub.

---

# 🚀 2. Elemento `on`

Define quando o workflow será executado.

---

## 📄 Exemplo

```yaml
on:
  push:
    branches: [ main ]

  pull_request:
    branches: [ main ]
```

---

## 📌 Nesse exemplo

O workflow executará quando:

- 🚀 Houver push na branch `main`
- 🔀 Um Pull Request for criado/atualizado para `main`

---

# ⚙️ 3. Elemento `jobs`

Contém as tarefas que serão executadas.

Cada job roda em um ambiente isolado.

---

## 📄 Exemplo

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Executar comando
        run: echo "Hello World!"
```

---

# 🧩 Estrutura Completa Mínima

## 📄 Workflow básico funcional

```yaml
name: Workflow Básico

on:
  push:
    branches: [ main ]

jobs:
  exemplo:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: echo "Workflow executado com sucesso!"
```

---

# 🧠 Entendendo o Fluxo

## 🔄 O que acontece nesse workflow

1. 🚀 Ocorre um push na branch `main`
2. ⚙️ O GitHub inicia o workflow
3. 🖥️ Um runner Ubuntu é criado
4. 📥 O repositório é baixado
5. 💬 O comando echo é executado
6. ✅ O workflow finaliza com sucesso

---

# 🧩 Componentes Detalhados

# ⚙️ Jobs

Cada workflow pode conter múltiplos jobs.

Por padrão:

```text
Jobs independentes executam em paralelo
```

---

## 📄 Exemplo

```yaml
jobs:
  primeiro-job:
    runs-on: ubuntu-latest

    steps:
      - name: Passo 1
        run: echo "Executando passo 1"

      - name: Passo 2
        run: echo "Executando passo 2"

  segundo-job:
    runs-on: windows-latest

    steps:
      - name: Job no Windows
        run: echo "Executando no Windows"
```

---

# 📌 Nesse exemplo

| Job | Ambiente |
|---|---|
| 🐧 `primeiro-job` | Ubuntu |
| 🪟 `segundo-job` | Windows |

Ambos podem executar simultaneamente.

---

# ⚡ Steps

Os `steps` representam as etapas individuais de um job.

---

# 📌 Tipos de Step

| Tipo | Descrição |
|---|---|
| ♻️ `uses` | Utiliza uma action pronta |
| 💻 `run` | Executa comandos shell |

---

## 📄 Exemplo

```yaml
steps:
  # Usando action
  - name: Checkout
    uses: actions/checkout@v4

  # Usando comando shell
  - name: Listar arquivos
    run: ls -la

  # Comando multi-linha
  - name: Comandos múltiplos
    run: |
      echo "Primeira linha"
      echo "Segunda linha"
      pwd
```

---

# 📂 Localização do Arquivo

Todos os workflows devem ficar dentro do diretório:

```text
.github/workflows/
```

---

## 📄 Estrutura

```text
.github/
└── workflows/
    └── meu-workflow.yml
```

---

# 📌 Extensões válidas

- ✅ `.yml`
- ✅ `.yaml`

---

# 🖥️ Runners

Cada job precisa de um ambiente de execução.

Isso é definido por:

```yaml
runs-on:
```

---

# 📌 Exemplos comuns

| Runner | Sistema |
|---|---|
| 🐧 `ubuntu-latest` | Linux Ubuntu |
| 🪟 `windows-latest` | Windows |
| 🍎 `macos-latest` | macOS |

---

# 🔄 Fluxo Visual de Execução

```text
Evento (push/PR)
        ↓
Workflow iniciado
        ↓
Runner criado
        ↓
Jobs executados
        ↓
Steps executados
        ↓
Resultado exibido no GitHub
```

---

# 🧪 Exemplo Prático Completo

## 📄 Workflow simples de CI

```yaml
name: Simple CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Exibir diretório
        run: pwd

      - name: Listar arquivos
        run: ls -la

      - name: Finalizar
        run: echo "CI executada com sucesso!"
```

---

# 🎯 Output Esperado

Após o push na branch `main`:

- 🚀 Workflow é iniciado automaticamente
- 🖥️ Runner Ubuntu é provisionado
- 📥 Código é baixado
- 📂 Arquivos do projeto são listados
- 💬 Mensagem final é exibida
- ✅ Status aparece na aba Actions

---

# ⚠️ Erros Frequentes

## ❌ Arquivo fora da pasta correta

```text
.github/workflows/
```

é obrigatório.

---

## ❌ Indentação YAML incorreta

YAML depende totalmente de espaços.

---

## ❌ Esquecer `runs-on`

Todo job precisa de um runner.

---

## ❌ Misturar `uses` e `run`

Cada step normalmente usa apenas um deles.

---

# 📌 Boas Práticas

## ✅ Recomendações

- 🏷️ Usar nomes descritivos
- 🧱 Separar jobs por responsabilidade
- 📄 Manter workflows pequenos inicialmente
- 💬 Nomear steps importantes
- 🧠 Evoluir gradualmente a pipeline

---

# 🏁 Conclusão

Um workflow básico com:

- 🏷️ `name`
- 🚀 `on`
- ⚙️ `jobs`

representa a fundação de toda automação no GitHub Actions.

Começar com estruturas simples e organizadas facilita a evolução futura para pipelines mais avançados envolvendo:

- 🧪 Testes automatizados
- 📦 Build
- 🚀 Deploy
- 🔒 Segurança
- 📊 Monitoramento

Dominar esses fundamentos é o primeiro passo para construir automações robustas, reutilizáveis e escaláveis.