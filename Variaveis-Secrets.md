# 🔐 Variáveis (`env` / `vars`) e Segredos: Escopos e Precedência

## 📘 Introdução

Variáveis e segredos são a base de configuração dos workflows no GitHub Actions.

Eles conectam:

- ⚙️ Configurações do repositório
- 🌐 Ambientes
- 🚀 Pipelines
- 🔑 Credenciais
- 🧩 Comportamentos dinâmicos

Entender:

- 📍 Onde declarar
- 🧠 Como funcionam os escopos
- ⚡ Como a precedência é aplicada
- 🛡️ Como usar segredos com segurança

evita comportamentos inesperados e reduz riscos de exposição de dados.

---

# 🧩 Definição

## ⚙️ `env`

Variáveis de ambiente definidas diretamente no YAML.

Podem existir em:

- 🌍 Workflow
- 🎯 Job
- 🧩 Step

---

## 📦 `vars`

Variáveis gerenciadas pela interface do GitHub.

Podem existir em:

- 🏢 Organização
- 📁 Repositório
- 🌐 Environment

São ideais para:

```text
Valores NÃO sensíveis
```

---

## 🔐 `secrets`

Armazenam informações confidenciais.

Exemplos:

- 🔑 Tokens
- 🔐 Senhas
- ☁️ Chaves cloud
- 🔒 API Keys

Possuem:

- 🛡️ Mascaramento automático
- 🚫 Proteção contra logs acidentais
- 🎯 Controle por escopo

---

# 🔗 Links Oficiais

## 📘 env (workflow syntax)

```text
https://docs.github.com/pt/actions/using-workflows/workflow-syntax-for-github-actions#env
```

---

## 📘 Contextos (`vars` e `secrets`)

```text
https://docs.github.com/pt/actions/learn-github-actions/contexts
```

---

## 📘 Condicionais (`if`)

```text
https://docs.github.com/pt/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idstepsif
```

---

# ⚙️ Funcionamento Técnico

# 🌍 Escopo de `env`

Variáveis `env` podem ser declaradas em três níveis:

```text
Workflow → Job → Step
```

---

# 📌 Workflow

Disponível globalmente.

```yaml
env:
  APP_NAME: demo
```

---

# 📌 Job

Disponível apenas dentro do job.

```yaml
jobs:
  build:
    env:
      LOG_LEVEL: warn
```

---

# 📌 Step

Disponível apenas naquele step.

```yaml
steps:
  - env:
      APP_NAME: override
```

---

# ⚡ Precedência de `env`

A regra é:

```text
Step > Job > Workflow
```

O escopo mais específico sempre vence.

---

# 🧠 Exemplo Visual

```text
Workflow:
  APP_NAME=demo

Job:
  APP_NAME=backend

Step:
  APP_NAME=api
```

Resultado no step:

```text
APP_NAME=api
```

---

# 📦 Funcionamento de `vars`

## 📌 Hierarquia

As `vars` seguem esta ordem:

```text
Environment > Repository > Organization
```

---

# 🌐 Exemplo

Mesmo nome:

```text
APP_ENV
```

Pode possuir valores diferentes:

- 🏢 Organização → `dev`
- 📁 Repositório → `staging`
- 🌐 Environment → `prod`

Quando o job usar:

```yaml
environment: prod
```

o valor do environment prevalece.

---

# 🔐 Funcionamento de `secrets`

## 📌 Escopos disponíveis

- 🏢 Organização
- 📁 Repositório
- 🌐 Environment

---

# 🛡️ Proteções automáticas

Segredos:

- 🚫 Não aparecem em logs
- 🔒 São mascarados automaticamente
- 🚫 Não ficam disponíveis em forks

---

# ⚠️ Importante

Nunca imprima secrets diretamente:

```bash
echo $API_TOKEN
```

---

# ✅ Forma correta

Mapear para `env:` e usar silenciosamente.

---

# 🔄 YAML vs Shell

## 📌 Dentro do YAML

Use:

```yaml
${{ env.VAR }}
```

---

## 📌 Dentro do shell

Use:

```bash
$VAR
```

---

# ⚠️ Diferença Importante

## YAML

Interpretado pelo GitHub Actions.

---

## Shell

Interpretado pelo sistema operacional do runner.

---

# 🛡️ Uso Seguro de Secrets

## ✅ Recomendado

```yaml
env:
  API_TOKEN: ${{ secrets.API_TOKEN }}
```

---

## 🚫 Evite

```yaml
run: echo "${{ secrets.API_TOKEN }}"
```

---

# ⚠️ PRs vindos de Forks

## 🚫 Segredos NÃO são disponibilizados

Isso evita exposição acidental para código não confiável.

---

# 🔄 Reutilização de Workflows

Ao usar:

```yaml
workflow_call
```

tenha cuidado com:

```yaml
secrets: inherit
```

---

# ✅ Melhor abordagem

Declarar explicitamente quais segredos serão aceitos.

---

# ⚠️ `if` e Secrets

Em algumas propriedades:

```yaml
if:
```

`secrets` pode não estar disponível.

---

# ✅ Recomendação

Use:

- `vars`
- `github`
- lógica no shell

---

# 📌 Exemplo recomendado

```yaml
if: ${{ vars.APP_ENV == 'prod' }}
```

---

# 🧠 Boas Práticas

## ✅ Recomendações

- 📍 Definir padrões no topo do workflow
- 🎯 Sobrescrever apenas quando necessário
- 🧩 Centralizar valores não sensíveis em `vars`
- 🔐 Separar segredos por ambiente
- 🚫 Nunca imprimir secrets
- 📄 Documentar variáveis importantes
- 🔄 Reutilizar nomes consistentes

---

# ❌ Más Práticas

## 🚫 Duplicar valores em vários lugares

Dificulta manutenção.

---

## 🚫 Misturar secrets e vars sem padrão

Confunde precedência.

---

## 🚫 Hardcode de credenciais

Nunca coloque tokens diretamente no YAML.

---

## 🚫 Exibir secrets em logs

Mesmo mascarados, isso é uma má prática.

---

# 🧪 Exemplo Prático

## 📄 Workflow Completo

```yaml
name: Aula 19 — Variáveis e Segredos

on:
  workflow_dispatch:

env:
  APP_NAME: demo-app
  LOG_LEVEL: info

jobs:
  build:
    runs-on: ubuntu-latest

    environment: prod

    env:
      LOG_LEVEL: warn
      APP_ENV: ${{ vars.APP_ENV }}

    steps:
      - name: Mostrar env herdado e precedence
        env:
          APP_NAME: step-override

        run: |
          echo "APP_NAME (step): ${APP_NAME}"
          echo "LOG_LEVEL (job): ${LOG_LEVEL}"
          echo "APP_ENV (vars): ${APP_ENV:-dev}"

      - name: Mapear segredo para o shell com segurança
        env:
          API_TOKEN: ${{ secrets.API_TOKEN }}

        run: |
          echo "Usando token em chamada"
          # curl -H "Authorization: Bearer ${API_TOKEN}" ...

      - name: Condicional usando vars
        if: ${{ vars.APP_ENV == 'prod' }}

        run: |
          echo "Executando etapa somente em prod"
```

---

# 🔍 O que observar?

# 📌 `APP_NAME`

## Workflow

```yaml
APP_NAME: demo-app
```

---

## Step

```yaml
APP_NAME: step-override
```

O step sobrescreve o valor global.

---

# 📌 `LOG_LEVEL`

## Workflow

```yaml
LOG_LEVEL: info
```

---

## Job

```yaml
LOG_LEVEL: warn
```

O valor do job prevalece no job inteiro.

---

# 📌 `APP_ENV`

Vem do contexto:

```yaml
vars.APP_ENV
```

seguindo a hierarquia:

```text
Environment > Repository > Organization
```

---

# 📌 `API_TOKEN`

O segredo:

- 🔐 É mapeado para `env`
- 🚫 Não é exibido
- 🛡️ É mascarado automaticamente

---

# 📌 Condicional

A lógica usa:

```yaml
vars.APP_ENV
```

em vez de `secrets`.

Isso evita limitações e exposição indevida.

---

# 📈 Benefícios da Estrutura Correta

## ✅ Segurança

Reduz risco de vazamento.

---

## ✅ Organização

Centraliza configuração.

---

## ✅ Reutilização

Facilita manutenção entre workflows.

---

## ✅ Previsibilidade

A precedência evita ambiguidades.

---

# ⚠️ Erros Frequentes

## ❌ Misturar YAML e shell

```yaml
$VAR
```

não funciona corretamente em todos os campos YAML.

---

## ❌ Duplicar variáveis

Cria inconsistência.

---

## ❌ Secrets em logs

Mesmo mascarados, continuam sendo risco.

---

## ❌ Ignorar precedência

Pode gerar comportamentos inesperados.

---

# 🏁 Conclusão

Dominar:

- ⚙️ `env`
- 📦 `vars`
- 🔐 `secrets`

significa entender:

- 📍 Escopos
- ⚡ Precedência
- 🛡️ Segurança
- 🔄 Herança

A estratégia recomendada é:

```text
Workflow → padrões globais
↓
Job → ajustes específicos
↓
Step → sobrescritas pontuais
```

Além disso:

- 📦 `vars` devem centralizar valores não sensíveis
- 🔐 `secrets` devem ficar segregados por ambiente
- 🛡️ Credenciais devem ser mapeadas para `env`
- 🚫 Segredos nunca devem ser impressos

Com essas práticas, seus workflows se tornam:

- 🔒 Mais seguros
- 🧠 Mais previsíveis
- 🛠️ Mais fáceis de manter
- 📈 Mais escaláveis
```