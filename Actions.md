# ♻️ O que são Actions

## 📘 Introdução

Actions são blocos reutilizáveis de automação utilizados dentro do GitHub Actions.

Elas permitem encapsular tarefas frequentes como:

- 📥 Checkout de código
- ⚙️ Setup de linguagem
- 🧪 Execução de testes
- 🏗️ Build de aplicações
- 🔒 Análise de segurança
- 🚀 Deploy

Ao reutilizar actions, reduzimos repetição de código, padronizamos processos e aumentamos a consistência operacional dos workflows.

Compreender estrutura, tipos, versionamento e boas práticas é essencial para construir pipelines modernos e sustentáveis.

---

# 🧩 Definição

Uma action é uma unidade versionada de execução declarativa consumida através da instrução:

```yaml
uses:
```

Ela é utilizada dentro de um `step` de workflow e pode:

- 📥 Receber inputs
- 📤 Produzir outputs
- 🌎 Definir variáveis de ambiente
- ⚙️ Executar comandos e scripts
- 🔗 Integrar serviços externos

---

# 🏷️ Tipos de Actions

Existem três tipos principais de actions no GitHub Actions.

---

## ⚡ 1. JavaScript Actions

Executadas diretamente no runner utilizando Node.js.

### ✅ Características

- Menor latência
- Execução rápida
- Acesso direto ao filesystem
- Integração com APIs do GitHub

### 📄 Exemplo

```yaml
runs:
  using: node20
  main: dist/index.js
```

### 📌 Ideal para

- Integrações com APIs
- Automações rápidas
- Manipulação de arquivos
- Processamento de dados

---

## 🐳 2. Docker Actions

Executadas dentro de containers Docker.

### ✅ Características

- Isolamento completo
- Dependências encapsuladas
- Ambiente consistente

### 📄 Exemplo

```yaml
runs:
  using: docker
  image: Dockerfile
```

### 📌 Ideal para

- Ferramentas nativas específicas
- Dependências complexas
- Toolchains customizadas

---

## 🧩 3. Composite Actions

Agrupam múltiplos steps em uma única action reutilizável.

### ✅ Características

- Não criam runtime próprio
- Encadeiam comandos e outras actions
- Melhoram legibilidade dos workflows

### 📄 Exemplo

```yaml
runs:
  using: composite

  steps:
    - run: npm run lint

    - run: npm test
```

### 📌 Ideal para

- Reutilização simples
- Padronização interna
- Sequências repetitivas de comandos

---

# ⚙️ Explicação Técnica

# 📄 Anatomia Básica

Toda action possui um arquivo principal:

```bash
action.yml
```

ou:

```bash
action.yaml
```

Esse arquivo define os metadados e comportamento da action.

---

## 📌 Estrutura básica

```yaml
name: Setup Ferramenta X

description: Instala e configura Ferramenta X

inputs:
  version:
    description: Versão desejada
    required: false
    default: 'latest'

runs:
  using: node20
  main: dist/index.js
```

---

# 📥 Inputs e Outputs

## 📥 Inputs

Inputs permitem parametrizar a execução da action.

### 📄 Exemplo

```yaml
inputs:
  version:
    required: true
```

### 📌 Utilização

- Configuração dinâmica
- Customização de comportamento
- Reutilização flexível

---

## 📤 Outputs

Outputs permitem compartilhar dados com steps posteriores.

### 📄 Definição em JavaScript

```javascript
core.setOutput('artifact-path', caminho)
```

### 📄 Consumo no workflow

```yaml
- name: Usar action
  id: setup
  uses: org/setup-ferramenta@v1
  with:
    version: 2.1.0

- name: Consumir output
  run: echo "Path: ${{ steps.setup.outputs.artifact-path }}"
```

---

# 🔍 Diferença entre Action e Step

Embora relacionados, possuem papéis diferentes.

| Conceito | Descrição |
|---|---|
| 🔹 Step | Instrução individual executada no job |
| ♻️ Action | Bloco reutilizável consumido via `uses:` |

---

# 🏷️ Versionamento e Referências

Actions são utilizadas no formato:

```yaml
uses: owner/repo@ref
```

---

## 📌 Exemplos

### ✅ Tag estável

```yaml
uses: actions/checkout@v4
```

### ⚠️ Branch mutável

```yaml
uses: actions/checkout@main
```

### 🔒 Commit SHA fixo

```yaml
uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608
```

---

## ✅ Boas práticas

- 🔒 Preferir SHA em ambientes críticos
- 📦 Utilizar versões estáveis (`@v4`)
- ⚠️ Evitar branches mutáveis em produção
- 🔄 Atualizar actions periodicamente

---

# 🔐 Segurança

Actions fazem parte da cadeia de supply chain do projeto.

Por isso, é importante aplicar práticas de segurança.

---

## ✅ Recomendações

- 🔍 Revisar a origem da action
- 🔒 Fixar versões confiáveis
- 🛡️ Reduzir permissões do `GITHUB_TOKEN`
- 🔄 Atualizar versões regularmente
- 📦 Preferir actions populares e auditadas

---

# ⚡ Performance

Cada tipo de action possui impactos diferentes em performance.

| Tipo | Performance |
|---|---|
| ⚡ JavaScript | Mais rápida |
| 🐳 Docker | Mais lenta devido ao container |
| 🧩 Composite | Leve e eficiente |

---

## 📌 Otimizações

- 📦 Cache de dependências
- 🐳 Cache de camadas Docker
- 🧩 Reutilização de composite actions
- ⚡ Evitar instalações repetitivas

---

# 🛠️ Quando Criar uma Action Própria

Nem sempre vale a pena criar uma action customizada.

---

## ✅ Criar action própria quando

- 🔗 Existe integração complexa com APIs
- ⚙️ Há lógica sofisticada
- 🐳 Dependências precisam de isolamento
- 📊 Existe processamento avançado

---

## ✅ Preferir Composite Action quando

- 🧩 Apenas agrupa comandos existentes
- 📏 Deseja padronizar workflows
- ♻️ Reutiliza steps repetitivos

---

# 📦 Publicação e Reuso Interno

Actions podem ser publicadas para reutilização.

---

## 📌 Boas práticas

- 📁 Repositório dedicado
- 🏷️ Versionamento semântico
- 📝 Changelog atualizado
- 🔄 Tags estáveis

---

## 📄 Exemplo de versionamento

```bash
git tag v1.2.0
git push origin v1.2.0
```

---

# 🧪 Exemplo Prático

Workflow utilizando actions do Marketplace e uma composite local.

## 📄 Workflow

```yaml
name: Exemplo Actions

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Instalar
        run: npm ci

      - name: Testar
        run: npm test -- --ci

      - name: Qualidade Interna
        uses: ./.github/actions/qualidade
        with:
          cobertura-minima: '80'
```

---

## 📄 Composite Action Interna

```yaml
name: Qualidade

runs:
  using: composite

  steps:
    - run: npm run lint

    - run: npm run coverage:check -- --min=${{ inputs.cobertura-minima }}

inputs:
  cobertura-minima:
    required: true
```

---

# 🎯 Benefícios das Actions

## ✅ Principais vantagens

- ♻️ Reutilização de lógica
- 📏 Padronização de pipelines
- ⚡ Redução de duplicação
- 🔒 Melhor controle de segurança
- 🚀 Maior produtividade
- 🧠 Workflows mais limpos

---

# 🏁 Conclusão

Actions elevam o nível de abstração dos workflows no GitHub Actions.

Elas encapsulam tarefas em unidades reutilizáveis e versionadas, promovendo:

- ♻️ Reuso
- 🔒 Segurança
- 📏 Padronização
- ⚡ Performance
- 🚀 Escalabilidade

Escolher corretamente entre JavaScript, Docker e Composite Actions reduz complexidade e facilita manutenção a longo prazo.

Criar actions apenas quando realmente agregam valor evita excesso de abstração e mantém o ecossistema simples, sustentável e eficiente.

O próximo passo natural é aprofundar-se em:

- 🧩 Criação de Composite Actions
- 🔐 Controle avançado de permissões
- 📦 Estratégias de caching
- 🚀 Publicação de actions no Marketplace