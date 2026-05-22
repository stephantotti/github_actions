# 🧾 YAML Básico para Workflows

## 📘 Introdução

Antes de combinar eventos, jobs e steps no GitHub Actions, é essencial compreender a sintaxe YAML.

Todos os workflows são definidos em arquivos:

```bash
.yml
```

ou

```bash
.yaml
```

Pequenos erros de estrutura, indentação ou formatação podem causar:

- ❌ Falhas silenciosas
- ⚠️ Parsing incorreto
- 🚫 Execução parcial do workflow
- 🔴 Quebra total da pipeline

Esta aula apresenta os fundamentos necessários para criar workflows seguros, legíveis e previsíveis.

---

# 🧩 Definição

YAML (**YAML Ain't Markup Language**) é um formato de serialização de dados legível por humanos.

Ele representa:

- 🔑 Pares chave–valor
- 📋 Listas
- 🧱 Estruturas hierárquicas

No GitHub Actions, YAML é utilizado para definir:

- 🚀 Eventos (`on`)
- 🧩 Jobs (`jobs`)
- ⚡ Steps (`steps`)
- 🌎 Variáveis (`env`)
- 🔐 Permissões (`permissions`)

---

# ⚙️ Estrutura Geral de um Workflow

## 📌 Estrutura típica

```yaml
name: CI Pipeline

on:
  push:

permissions:
  contents: read

env:
  NODE_VERSION: '20'

jobs:
  build:
    runs-on: ubuntu-latest
```

---

# 🧱 Componentes Principais

| Elemento | Função |
|---|---|
| 🏷️ `name` | Nome exibido no GitHub |
| 🚀 `on` | Eventos que disparam o workflow |
| 🔐 `permissions` | Escopo do GITHUB_TOKEN |
| 🌎 `env` | Variáveis globais |
| 🧩 `jobs` | Conjunto de jobs executados |

---

# 📏 Indentação

YAML depende totalmente da indentação.

## ✅ Regra principal

- ✔️ Usar ESPAÇOS
- ❌ Nunca usar TAB

---

## 📌 Recomendação

Utilizar:

```text
2 espaços por nível
```

---

## ❌ Exemplo incorreto

```yaml
jobs:
 build:
    runs-on: ubuntu-latest
```

---

## ✅ Correção

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
```

---

# 🔑 Chaves e Valores

## 📌 Estrutura básica

```yaml
name: CI

runs-on: ubuntu-latest
```

---

# 📝 Strings

Strings podem ser escritas de diferentes formas.

---

## ✅ Sem aspas

```yaml
name: CI
```

---

## ✅ Aspas duplas

Permitem escape e interpolação.

```yaml
name: "Meu Workflow"
```

---

## ✅ Aspas simples

Mantêm conteúdo literal.

```yaml
name: 'Texto literal: ${{ nao_expande }}'
```

---

# 📋 Listas

YAML suporta listas inline e multilinha.

---

## ✅ Inline

```yaml
branches: [ main, develop ]
```

---

## ✅ Multilinha

```yaml
branches:
  - main
  - develop
```

---

## 📌 Quando usar cada formato

| Formato | Melhor uso |
|---|---|
| 📏 Inline | Listas curtas |
| 📋 Multilinha | Listas maiores e mais legíveis |

---

# 🧱 Mapeamentos Aninhados

Estruturas podem conter outros blocos internos.

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main
```

---

# 💬 Comentários

Comentários começam com:

```yaml
#
```

---

## 📄 Exemplo

```yaml
# Nome exibido no GitHub
name: CI
```

---

# 📄 Blocos Multilinha

## 📌 Preservar quebra de linha (`|`)

```yaml
run: |
  echo "Linha 1"
  echo "Linha 2"
```

---

## 📌 Dobrar linhas (`>`)

```yaml
run: >
  echo "Linha 1"
  echo "Linha 2"
```

Resultado:

```text
echo "Linha 1" echo "Linha 2"
```

---

# 🧠 Expressões do GitHub Actions

Expressões como:

```yaml
${{ github.repository }}
```

não fazem parte do YAML.

Elas são avaliadas posteriormente pelo mecanismo do GitHub Actions.

---

## 📄 Exemplo

```yaml
run: echo "${{ github.repository }}"
```

---

# 🌎 Variáveis de Ambiente (`env`)

Variáveis podem existir em três níveis:

- 🌍 Workflow
- 🧩 Job
- ⚡ Step

---

# 📄 Exemplo Completo

```yaml
env:
  NODE_VERSION: '18'

jobs:
  build:
    env:
      APP_MODE: test

    steps:
      - name: Exemplo
        env:
          LOCAL_FLAG: enabled

        run: echo "${{ env.APP_MODE }} / $LOCAL_FLAG"
```

---

# 📌 Ordem de prioridade

```text
Step > Job > Workflow
```

O nível mais específico sobrescreve o mais global.

---

# 🧩 Estrutura de Jobs

Cada job normalmente contém:

- 🖥️ `runs-on`
- ⚡ `steps`
- 🔗 `needs`
- 🌎 `env`
- 📦 `strategy`

---

## 📄 Exemplo

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Instalar dependências
        run: npm ci
```

---

# ⚡ Estrutura de Steps

Cada step é um item da lista `steps:`.

---

## 📌 Principais propriedades

| Propriedade | Função |
|---|---|
| ♻️ `uses` | Executa uma action |
| ⚡ `run` | Executa comando shell |
| 🏷️ `name` | Nome exibido |
| 🔀 `if` | Condição opcional |

---

# 📄 Exemplo Completo

```yaml
steps:
  - name: Obter código
    uses: actions/checkout@v4

  - name: Testes
    run: npm test

  - name: Condicional
    if: github.event_name == 'pull_request'
    run: echo "Rodando apenas em PR"
```

---

# ⚠️ Erros Frequentes

## ❌ Misturar tabs e espaços

Causa falha de parsing.

---

## ❌ Esquecer `:`

```yaml
name CI
```

---

## ❌ Indentação incorreta

Estrutura hierárquica inválida.

---

## ❌ Duplicar chaves

```yaml
env:
  NODE: 18

env:
  JAVA: 17
```

O parser pode sobrescrever silenciosamente.

---

## ❌ Expressões dentro de aspas simples

```yaml
'${{ github.repository }}'
```

Permanece literal e não expande.

---

# 🧪 Validação Rápida

## ✅ Ferramentas úteis

- VSCode YAML Extension
- YAML Linters
- GitHub Actions Parser

---

## ✅ Boas práticas

- Fazer commits pequenos
- Validar incrementalmente
- Evitar mudanças massivas no workflow
- Testar parsing antes de expandir lógica

---

# 🧪 Exemplo Prático

Workflow mínimo demonstrando:

- 📋 Listas
- 🧱 Mapeamentos
- 🌎 Variáveis
- ⚡ Steps
- 🔀 Condições

---

## 📄 Workflow Completo

```yaml
name: Exemplo YAML Básico

on:
  push:
    branches: [ main ]

env:
  APP_NAME: demo

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Mostrar contexto
        run: |
          echo "Repo: ${{ github.repository }}"
          echo "App: $APP_NAME"

      - name: Executar somente em push
        if: github.event_name == 'push'
        run: echo "Disparado por push"
```

---

# 🎯 Output Esperado

Ao executar esse workflow:

- 🚀 Workflow dispara em push para `main`
- 📥 Código é baixado automaticamente
- 🌎 Variáveis de ambiente são carregadas
- 🧠 Contextos GitHub são expandidos
- ⚡ Steps executam sequencialmente
- 📄 Logs aparecem na aba Actions

---

# 📌 Boas Práticas YAML

## ✅ Recomendações importantes

- 📏 Padronizar indentação
- 📄 Usar nomes claros
- 🧩 Separar jobs por responsabilidade
- 💬 Comentar workflows complexos
- ⚡ Evitar duplicação
- 🔍 Validar frequentemente

---

# 🏁 Conclusão

YAML é a base estrutural do GitHub Actions.

Embora simples visualmente, sua sintaxe depende fortemente de:

- 📏 Indentação correta
- 🧱 Hierarquia consistente
- 📋 Estrutura organizada

Dominar os fundamentos YAML permite criar workflows:

- 🚀 Confiáveis
- 📄 Legíveis
- 🧠 Manuteníveis
- ⚡ Escaláveis

Com uma base sólida de YAML, torna-se muito mais fácil evoluir para pipelines avançados com múltiplos jobs, matrix strategies, caching, deploy automatizado e integrações complexas.