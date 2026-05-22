# 🚀 Eventos de Trigger no GitHub Actions

## 📘 Introdução

Os eventos de trigger (gatilhos) são responsáveis por determinar **quando** um workflow do GitHub Actions será executado.

Eles representam o mecanismo que conecta ações realizadas no repositório à execução automática dos pipelines.

Com triggers bem configurados, é possível:

- ⚡ Automatizar builds e testes
- 🚀 Realizar deploys automáticos
- 📦 Executar pipelines sob demanda
- 🧪 Rodar verificações periódicas
- 🔄 Integrar processos de revisão e CI/CD

A flexibilidade dos eventos permite criar workflows extremamente eficientes e econômicos.

---

# 🧩 Definição

Os eventos de trigger são configurações declarativas dentro do bloco:

```yaml
on:
```

Eles definem quais ações ou eventos devem iniciar a execução de um workflow.

Cada evento possui:

- ⚙️ Comportamentos específicos
- 🎯 Filtros opcionais
- 🔀 Condições de execução
- 📋 Contextos próprios

---

# ⚙️ Estrutura Básica

## 📄 Exemplo simples

```yaml
on:
  push:
```

Neste caso, o workflow será executado sempre que houver um push.

---

# 🚀 1. Evento `push`

O evento `push` é acionado quando commits são enviados ao repositório.

---

## 📄 Exemplo

```yaml
on:
  push:
    branches:
      - main
      - develop

    paths:
      - 'src/**'
      - '!src/tests/**'
```

---

## 📌 O que esse trigger faz

Executa apenas quando:

- 🚀 O push ocorre em `main` ou `develop`
- 📁 Arquivos dentro de `src/` são alterados
- ❌ Exceto arquivos dentro de `src/tests/`

---

# 🔀 2. Evento `pull_request`

Executa workflows relacionados a Pull Requests.

---

## 📄 Exemplo

```yaml
on:
  pull_request:
    types:
      - opened
      - synchronize
      - closed

    branches:
      - main

    paths-ignore:
      - 'docs/**'
      - '*.md'
```

---

## 📌 Eventos comuns de PR

| Tipo | Descrição |
|---|---|
| 🆕 `opened` | PR criado |
| 🔄 `synchronize` | Novos commits enviados |
| ❌ `closed` | PR fechado |
| ✏️ `edited` | PR editado |
| 👀 `review_requested` | Review solicitado |

---

# ⏰ 3. Evento `schedule`

Permite execução agendada usando sintaxe CRON.

---

## 📄 Exemplo

```yaml
on:
  schedule:
    - cron: '0 2 * * 1-5'

    - cron: '0 0 * * 0'
```

---

## 📌 Interpretação

| Cron | Execução |
|---|---|
| `0 2 * * 1-5` | Segunda a sexta às 2h |
| `0 0 * * 0` | Domingo à meia-noite |

---

# 🧠 Estrutura CRON

```text
* * * * *
│ │ │ │ │
│ │ │ │ └── Dia da semana
│ │ │ └──── Mês
│ │ └────── Dia do mês
│ └──────── Hora
└────────── Minuto
```

---

# 🖱️ 4. Evento `workflow_dispatch`

Permite executar workflows manualmente pela interface do GitHub.

---

## 📄 Exemplo

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'staging'

        type: choice

        options:
          - staging
          - production

      version:
        description: 'Version number'
        required: false
        type: string
```

---

# 📌 Benefícios

- 🖱️ Execução manual sob demanda
- 🧪 Deploy controlado
- ⚙️ Recebimento de parâmetros
- 🚀 Ideal para releases e hotfixes

---

# 🐞 5. Evento `issues`

Executa automações relacionadas às issues do GitHub.

---

## 📄 Exemplo

```yaml
on:
  issues:
    types:
      - opened
      - labeled
      - closed
```

---

## 📌 Casos de uso

- 🤖 Respostas automáticas
- 🏷️ Automação de labels
- 📢 Notificações
- 📊 Integração com sistemas externos

---

# 🎯 Filtros de Branches e Paths

Filtros permitem controlar exatamente quando o workflow deve executar.

---

# 🌿 Filtro de Branches

## 📄 Exemplo

```yaml
branches:
  - main

  - 'release/**'

  - '!feature/experimental'
```

---

## 📌 Regras

| Padrão | Significado |
|---|---|
| `main` | Apenas branch main |
| `release/**` | Todas branches release |
| `!feature/experimental` | Exclui branch específica |

---

# 📁 Filtro de Paths

## 📄 Exemplo

```yaml
paths:
  - 'src/**'

  - 'package*.json'
```

---

## 📌 Executa apenas quando

- Arquivos dentro de `src/` mudarem
- `package.json` ou similares forem alterados

---

# 🚫 paths-ignore

Ignora determinados arquivos.

## 📄 Exemplo

```yaml
paths-ignore:
  - 'docs/**'

  - '**.md'
```

---

## 📌 Benefício

Evita execução desnecessária do pipeline para mudanças irrelevantes.

---

# 🧪 Exemplo Prático Completo

Workflow utilizando múltiplos triggers.

---

## 📄 Workflow

```yaml
name: Multi-Trigger Pipeline

on:
  push:
    branches: [ main ]

    paths:
      - 'src/**'
      - 'package*.json'

  pull_request:
    branches: [ main ]

    types:
      - opened
      - synchronize

  schedule:
    - cron: '0 9 * * 1'

  workflow_dispatch:
    inputs:
      run_tests:
        description: 'Execute tests'
        type: boolean
        default: true

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        if: github.event_name == 'pull_request' || inputs.run_tests
        run: npm test

      - name: Build project
        run: npm run build
```

---

# 🔍 Explicando o Workflow

## 🚀 O workflow será executado quando

- Houver push na branch `main`
- Um Pull Request for aberto ou atualizado
- Toda segunda-feira às 9h
- Manualmente via interface GitHub

---

## ⚡ Condição no step de testes

```yaml
if: github.event_name == 'pull_request' || inputs.run_tests
```

Executa testes apenas:

- 🔀 Em Pull Requests
- 🖱️ Ou quando parâmetro manual estiver habilitado

---

# 🎯 Output Esperado

Após execução:

- 🚀 Pipeline inicia automaticamente
- 📥 Código é baixado
- ⚙️ Node.js é configurado
- 📦 Dependências instaladas
- 🧪 Testes executados condicionalmente
- 🏗️ Projeto compilado

---

# 📌 Boas Práticas

## ✅ Recomendações importantes

- 🎯 Usar filtros para evitar execuções desnecessárias
- 🌿 Restringir branches críticas
- 📁 Filtrar paths relevantes
- 🧠 Separar workflows por objetivo
- 🔄 Evitar triggers excessivos
- 🔐 Controlar workflows manuais sensíveis

---

# ⚠️ Erros Frequentes

## ❌ Falta de filtros

Executa pipeline em qualquer alteração.

---

## ❌ Uso incorreto de CRON

Horários inesperados devido ao UTC.

---

## ❌ Trigger excessivo

Pipeline executando centenas de vezes sem necessidade.

---

## ❌ Misturar lógica complexa no trigger

Dificulta manutenção e leitura.

---

# 🏁 Conclusão

Os eventos de trigger são o mecanismo central de automação do GitHub Actions.

Eles permitem controlar com precisão:

- 🚀 Quando executar workflows
- 🌿 Em quais branches
- 📁 Para quais arquivos
- 🧪 Sob quais condições

Combinando eventos, filtros e condições corretamente, é possível criar pipelines:

- ⚡ Mais rápidos
- 💰 Mais econômicos
- 🧠 Mais inteligentes
- 🔒 Mais seguros

A qualidade da automação depende diretamente da forma como os gatilhos são planejados e organizados.