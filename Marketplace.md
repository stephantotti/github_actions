# 🧩 Marketplace com Milhões de Actions Reutilizáveis

## 📘 Introdução

O GitHub Marketplace de Actions é um ecossistema massivo de componentes reutilizáveis que aceleram significativamente a construção de pipelines de CI/CD.

Ele fornece blocos prontos para tarefas como:

- 📥 Checkout de código
- ⚙️ Setup de linguagens
- 🧪 Testes automatizados
- 🏗️ Build e compilação
- 🔒 Segurança e análise de código
- 📦 Containerização
- 🚀 Deploy em cloud

Esta aula foca no uso estratégico, seleção criteriosa e governança de actions públicas.

> ⚠️ Conceitos como estrutura interna de actions, tipos e inputs/outputs já foram abordados anteriormente.

---

# 🧩 Definição

O GitHub Marketplace para Actions é um catálogo público onde mantenedores publicam ações versionadas para reutilização.

Ele permite:

- ♻️ Reuso global de automações
- ⚡ Aceleração de pipelines
- 📏 Padronização entre projetos
- 🔗 Integração com serviços externos

As actions são consumidas via referência:

```yaml
owner/repo@ref
```

Onde `ref` pode ser:

- 🏷️ Tag (ex: v4)
- 🌿 Branch
- 🔒 Commit SHA (recomendado em ambientes críticos)

---

# ⚙️ Explicação Técnica

## 🔄 Resolução e execução de actions

Quando um workflow é executado:

1. O runner identifica a action
2. Baixa o repositório correspondente
3. Resolve o `ref` (tag, branch ou SHA)
4. Lê o arquivo `action.yml`
5. Executa conforme definição interna

---

## 🔐 Boas práticas de referência

- 🏷️ Preferir versões estáveis (`@v4`)
- 🔒 Usar SHA em ambientes críticos
- ⚠️ Evitar `@main` em produção

---

# 🚀 Benefícios do Marketplace

## ⚡ 1. Velocidade

- Reduz código repetitivo
- Simplifica setup de ferramentas
- Acelera pipelines significativamente

---

## 🧪 2. Confiabilidade

- Actions populares são amplamente testadas
- Usadas por milhares de projetos
- Menor chance de erro em implementação comum

---

## 🧩 3. Especialização

- Integrações complexas prontas (AWS, Docker, segurança)
- Encapsulamento de boas práticas
- Menos necessidade de código customizado

---

## 🔄 4. Evolução contínua

- Atualizações feitas pelos mantenedores
- Correções automáticas de bugs
- Novos recursos sem alteração no workflow

---

## 📏 5. Padronização

- Mesma abordagem entre repositórios
- Redução de inconsistências
- Facilita manutenção em equipe

---

# ⚠️ Riscos e Mitigação

## 🔐 1. Supply chain attack

- Action pode ser comprometida

**Mitigação:**
- Fixar SHA
- Revisar código fonte

---

## 🔄 2. Breaking changes

- Atualizações major podem quebrar pipelines

**Mitigação:**
- Testar em ambiente isolado
- Controlar upgrades de versão

---

## 🧩 3. Dependência excessiva

- Uso exagerado de actions genéricas

**Mitigação:**
- Criar actions internas quando necessário
- Evitar dependência cega do marketplace

---

## ⚡ 4. Performance

- Encadeamento excessivo pode aumentar tempo de execução

**Mitigação:**
- Evitar redundâncias
- Usar cache quando possível

---

# 📊 Critérios de Seleção

Antes de usar uma action do marketplace, avaliar:

- ⭐ Popularidade e adoção
- 🔄 Frequência de atualização
- 📚 Qualidade da documentação
- 🧪 Cobertura de uso real (issues, comunidade)
- 🔐 Segurança do repositório
- 📦 Estratégia de versionamento

---

# 🏢 Governança Organizacional

Em ambientes corporativos:

## 📌 Boas práticas

- 📋 Manter lista de actions aprovadas
- 🔍 Revisar novas dependências externas
- 🧪 Validar via pipeline interno
- 🏷️ Padronizar versões usadas
- 🔐 Restringir permissões de execução

---

## 🧠 Objetivo da governança

- Evitar uso descontrolado de dependências externas
- Garantir segurança da cadeia de CI/CD
- Manter consistência entre projetos

---

# 🧪 Exemplo Prático

Pipeline combinando actions do marketplace + composite interna:

```yaml
name: Build and Scan

on: [push, pull_request]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Run Tests
        run: npm test -- --ci

      - name: Upload Coverage
        uses: codecov/codecov-action@v4
        with:
          fail_ci_if_error: true

      - name: Security Scan Metadata
        uses: dependabot/fetch-metadata@v2
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Container Build
        uses: docker/build-push-action@v5
        with:
          context: .
          push: false
          tags: my-app:ci

      - name: Quality Gate Interno
        uses: ./.github/actions/quality-gate
        with:
          min-coverage: '80'
          lint-level: 'strict'
```

---

# 🧩 Composite Action Interna

```yaml
name: Quality Gate

runs:
  using: composite

  steps:
    - run: npm run lint

    - run: npm run coverage:check -- --min=$MIN
      env:
        MIN: ${{ inputs.min-coverage }}

    - run: echo "Qualidade validada"

inputs:
  min-coverage:
    required: true

  lint-level:
    required: false
```

---

# ⚖️ Comparação: Sem Action vs Com Action

## ❌ Sem action (manual)

- Instalação manual de Node
- Configuração de PATH
- Setup de cache manual
- Maior risco de erro
- Mais linhas de código

---

## ✅ Com action

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
    cache: npm

- run: npm ci
```

---

## 🐳 Docker build manual vs action

### ❌ Manual

- build
- login
- tag
- push

### ✅ Com action

```yaml
- uses: docker/build-push-action@v5
  with:
    context: .
    push: true
    tags: registry/app:ci
```

---

## 📌 Principais ganhos

- 📉 Menos código
- 🧠 Menor complexidade
- 🔒 Menos pontos de falha
- ⚡ Melhor performance
- ♻️ Reuso de soluções maduras

---

# 🏁 Conclusão

O GitHub Marketplace é um acelerador estratégico para CI/CD moderno.

Ele permite:

- ⚡ Acelerar pipelines
- 📏 Padronizar processos
- 🔐 Reutilizar soluções seguras
- 🧩 Reduzir complexidade operacional

No entanto, seu uso eficiente depende de:

- 🔍 Seleção criteriosa
- 🔒 Controle de versão rigoroso
- 🏢 Governança em ambientes corporativos
- 🧠 Equilíbrio entre reuso e código interno

A maturidade em CI/CD passa não apenas por usar actions, mas por saber **quando confiar no marketplace e quando internalizar soluções críticas**.