# 🔐 Permissões e GITHUB_TOKEN: Menor Privilégio

## 📘 Introdução

O `GITHUB_TOKEN` é criado automaticamente em cada execução do GitHub Actions e herda um conjunto de permissões padrão.

Definir essas permissões seguindo o princípio do:

```text
Menor Privilégio
```

reduz riscos de segurança, evita escritas indevidas em Pull Requests e melhora a governança dos pipelines.

Esta aula apresenta:

- 🔐 O que é o `GITHUB_TOKEN`
- 🛡️ Como funciona o bloco `permissions`
- ⚙️ Escopos mais utilizados
- 🚨 Riscos em PRs de forks
- 🌐 Uso com GitHub Pages e OIDC
- ✅ Boas práticas de segurança

---

# 🧩 Definição

## 🔑 O que é o GITHUB_TOKEN?

O `GITHUB_TOKEN` é uma credencial efêmera criada automaticamente pelo GitHub durante a execução do workflow.

Ele permite:

- 📦 Acessar o repositório
- 📝 Interagir com Pull Requests
- 🔍 Consumir APIs do GitHub
- 🚀 Realizar deploys
- 📄 Publicar artifacts

Tudo isso sem expor um token pessoal (PAT).

---

# ⚙️ Bloco `permissions`

O bloco:

```yaml
permissions:
```

define exatamente o que o `GITHUB_TOKEN` pode fazer.

Pode ser configurado:

- 🌍 Globalmente (workflow inteiro)
- 🎯 Individualmente por job

---

# 🛡️ Princípio do Menor Privilégio

## 📌 Regra principal

Conceda apenas o mínimo necessário.

---

# ❌ Evite

```yaml
permissions: write-all
```

---

# ✅ Prefira

```yaml
permissions:
  contents: read
```

e eleve permissões apenas quando necessário.

---

# ⚙️ Funcionamento Técnico

# 🌍 Permissões Globais

Definem o padrão do workflow inteiro.

---

## 📄 Exemplo

```yaml
permissions:
  contents: read
```

Todos os jobs herdarão esse acesso.

---

# 🎯 Sobrescrita por Job

Cada job pode elevar ou reduzir permissões.

---

## 📄 Exemplo

```yaml
jobs:
  comentar:
    permissions:
      pull-requests: write
```

---

# 🔑 Escopos Mais Utilizados

| Permissão | Função |
|---|---|
| `contents` | Ler/escrever no repositório |
| `pull-requests` | Comentar ou atualizar PRs |
| `checks` | Publicar checks |
| `statuses` | Atualizar status |
| `deployments` | Criar deployments |
| `pages` | Publicar GitHub Pages |
| `id-token` | Autenticação OIDC |

---

# 📌 contents

## 🔍 Leitura mínima

```yaml
contents: read
```

Usado na maioria dos workflows.

---

## ✍️ Escrita

```yaml
contents: write
```

Permite:

- Criar tags
- Criar releases
- Atualizar arquivos

---

# 📌 pull-requests

Permite:

- 💬 Comentar em PRs
- 🏷️ Alterar labels
- ✏️ Atualizar PRs

---

# 📌 pages

Necessário para deploy no GitHub Pages.

```yaml
pages: write
```

---

# 📌 id-token

Usado para autenticação federada OIDC.

```yaml
id-token: write
```

Muito comum em:

- ☁️ AWS
- ☁️ Azure
- ☁️ GCP

---

# ⚠️ Segurança em Pull Requests

## 🚨 PRs vindos de forks

Quando o evento vem de um fork:

- 🔒 Segredos NÃO são disponibilizados
- 🔒 Permissões são reduzidas
- 🛡️ Escritas são bloqueadas

Isso protege o repositório base.

---

# ⚠️ Evento `pull_request_target`

## 🚨 Atenção

```yaml
pull_request_target
```

executa no contexto do repositório base.

Isso significa:

- 🔓 Pode acessar segredos
- 🔓 Pode ter permissões elevadas

---

# 🛡️ Recomendação

Use apenas quando realmente necessário e com revisão rigorosa.

---

# 🤖 Dependabot

PRs do Dependabot possuem contexto próprio.

---

## 📌 Importante

Evite depender de:

- 🔐 Secrets do repositório
- ✍️ Escritas sensíveis

---

# 🌐 GitHub Pages

Deploys oficiais normalmente exigem:

```yaml
pages: write
id-token: write
```

---

# ⚡ Estratégia Recomendada

## ✅ Fluxo seguro

```text
Workflow inteiro → permissões mínimas
↓
Job específico → elevação pontual
```

---

# 🧠 Boas Práticas

## ✅ Recomendações

- 🔐 Começar sempre com `contents: read`
- 🎯 Elevar permissões apenas por job
- 🚫 Evitar escrita em forks
- 🛡️ Restringir escopos ao mínimo
- 🔍 Auditar permissões regularmente
- 📦 Separar jobs de leitura e deploy

---

# ❌ Más Práticas

## 🚫 Permissões globais excessivas

```yaml
permissions: write-all
```

---

## 🚫 Escrita em PRs externos

Risco elevado de supply chain attack.

---

## 🚫 Misturar deploy e build no mesmo job

Aumenta superfície de ataque.

---

# 🧪 Exemplo 1 — Workflow com Permissões Mínimas

## 📄 YAML Completo

```yaml
name: Aula 18 — Permissões e GITHUB_TOKEN

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Testes de build (sem escrita)
        run: |
          echo "Executando testes..."

  comment:
    # Só comenta quando NÃO for fork
    if: ${{ github.event.pull_request.head.repo.fork == false }}

    runs-on: ubuntu-latest

    permissions:
      contents: read
      pull-requests: write

    steps:
      - name: Comentar no PR
        run: |
          echo "Comentando no PR via API"
```

---

# 🔍 O que esse workflow faz?

## 📌 Job `build`

- 🔒 Apenas leitura
- 🧪 Executa testes

---

## 📌 Job `comment`

- 💬 Pode comentar em PR
- 🚫 Não executa em forks
- 🎯 Eleva permissões somente nesse job

---

# 🧪 Exemplo 2 — Deploy GitHub Pages

## 📄 YAML Completo

```yaml
name: Deploy de Pages (exemplo)

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  deploy-pages:
    runs-on: ubuntu-latest

    permissions:
      pages: write
      id-token: write

    environment: github-pages

    steps:
      - name: Preparar artefatos
        run: |
          echo "Gerando site estático..."

      - name: Upload para Pages
        run: |
          echo "Enviando artefato..."
```

---

# 🌐 O que acontece aqui?

## 📌 Workflow inteiro

Possui apenas:

```yaml
contents: read
```

---

## 📌 Job de deploy

Recebe permissões extras:

```yaml
pages: write
id-token: write
```

Somente no momento necessário.

---

# 📈 Benefícios do Menor Privilégio

## ✅ Segurança

Reduz superfície de ataque.

---

## ✅ Governança

Facilita auditoria e compliance.

---

## ✅ Isolamento

Problemas em um job não comprometem o workflow inteiro.

---

## ✅ Previsibilidade

Cada job possui escopo claramente definido.

---

# ⚠️ Erros Frequentes

## ❌ Permissões amplas por comodidade

Muito comum em workflows iniciantes.

---

## ❌ Uso incorreto de `pull_request_target`

Pode expor secrets para código não confiável.

---

## ❌ Misturar leitura e escrita

Aumenta risco operacional.

---

## ❌ Deploy com permissões globais

Permissões elevadas devem ficar isoladas.

---

# 🔗 Links Oficiais

## 📘 permissions (workflow syntax)

```text
https://docs.github.com/pt/actions/using-workflows/workflow-syntax-for-github-actions#permissions
```

---

## 📘 GITHUB_TOKEN e autenticação automática

```text
https://docs.github.com/pt/actions/security-guides/automatic-token-authentication
```

---

# 🏁 Conclusão

Definir permissões explícitas e mínimas no GitHub Actions melhora significativamente:

- 🔐 Segurança
- 📊 Governança
- 🧠 Clareza operacional
- 🛡️ Proteção contra abuso

A abordagem recomendada é:

```text
Começar com leitura mínima
↓
Elevar permissões apenas onde necessário
↓
Isolar escrita e deploy em jobs específicos
```

Além disso:

- 🚫 PRs de forks devem ser tratados como não confiáveis
- 🎯 Escopos elevados devem ser temporários e localizados
- 🌐 Deploys (Pages/OIDC) devem possuir permissões específicas

Essas práticas reduzem superfície de ataque, aumentam previsibilidade e tornam pipelines mais seguras, sustentáveis e auditáveis.