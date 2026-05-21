# 🚀 GitHub Actions

## 📘 Introdução

GitHub Actions é uma plataforma poderosa de automação que revoluciona a forma como desenvolvemos e implantamos software. Integrada nativamente ao GitHub, ela permite automatizar fluxos de trabalho diretamente no repositório, eliminando a necessidade de ferramentas externas complexas.

Com GitHub Actions, é possível automatizar testes, builds, deploys, análise de código, geração de releases e diversas outras tarefas essenciais no ciclo de desenvolvimento moderno.

---

# 🧩 Definição

GitHub Actions é uma plataforma de automação que permite criar, personalizar e executar fluxos de trabalho de desenvolvimento de software diretamente no repositório GitHub.

Trata-se de um sistema de **CI/CD (Continuous Integration/Continuous Deployment)** baseado em eventos, que responde automaticamente a ações realizadas no repositório.

## 📌 Componentes principais

- ⚙️ **Workflows:** processos automatizados configurados em arquivos YAML
- ♻️ **Actions:** tarefas individuais reutilizáveis
- 🧱 **Jobs:** conjuntos de steps executados em runners
- 🖥️ **Runners:** máquinas virtuais responsáveis pela execução

---

# ⚙️ Explicação Técnica

Os workflows do GitHub Actions são definidos em arquivos YAML localizados na pasta:

```bash
.github/workflows/
```

## 📄 Exemplo de Workflow

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
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Configurar Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'corretto'

      - name: Executar testes
        run: ./mvnw test

      - name: Build da aplicação
        run: ./mvnw clean package
```

---

# 🧪 Exemplo Prático

Vamos criar um workflow básico para uma aplicação Spring Boot.

```yaml
name: Spring Boot CI/CD

on:
  push:
    branches: [ main, develop ]

  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'corretto'

      - name: Cache Maven dependencies
        uses: actions/cache@v4
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}

      - name: Run tests
        run: ./mvnw test

      - name: Generate test report
        uses: dorny/test-reporter@v1
        if: success() || failure()
        with:
          name: Maven Tests
          path: target/surefire-reports/*.xml
          reporter: java-junit
```

---

# 🎯 Output Esperado

Ao executar este workflow, teremos:

- 🚀 Trigger automático em pushes e pull requests
- 🖥️ Ambiente Ubuntu configurado automaticamente
- ☕ Java 17 instalado no runner
- 📦 Cache inteligente das dependências Maven
- 🧪 Execução automatizada dos testes
- 📊 Relatórios detalhados de testes
- ✅ Feedback visual de sucesso ou falha no GitHub

---

# 🌟 Benefícios da Automação de Workflows

## 🔗 1. Integração Nativa

- Sem necessidade de ferramentas externas
- Integração direta com repositórios GitHub
- Acesso automático a issues, PRs e releases

---

## 📈 2. Escalabilidade

- Runners gratuitos para projetos públicos
- Possibilidade de usar runners self-hosted
- Execução paralela de workflows

---

## 🌎 3. Ecossistema Rico

- Marketplace com milhares de actions prontas
- Grande comunidade ativa
- Integrações com AWS, Azure, Docker, Kubernetes e muito mais

---

## 🧠 4. Flexibilidade

- Suporte a múltiplas linguagens
- Workflows condicionais
- Execução baseada em eventos
- Pipelines personalizados

---

# ⚖️ Comparação com Outras Ferramentas de CI/CD

| Ferramenta | GitHub Actions | Jenkins | GitLab CI | Azure DevOps |
|---|---|---|---|---|
| 🏠 Hospedagem | Nativa GitHub | Self-hosted | Nativa GitLab | Cloud/On-premise |
| ⚙️ Configuração | YAML simples | Interface complexa | YAML | YAML/Visual |
| 🛒 Marketplace | Extenso | Plugins | Limitado | Médio |
| 💰 Custo | Gratuito (limitado) | Gratuito | Freemium | Pago |
| 📚 Curva de aprendizado | Baixa | Alta | Média | Média |

---

# 🏁 Conclusão

GitHub Actions representa uma evolução natural no universo DevOps, oferecendo uma solução moderna, integrada e acessível para automação de workflows.

Sua facilidade de uso, combinada com a integração nativa ao ecossistema GitHub, torna a plataforma uma excelente escolha tanto para iniciantes quanto para equipes experientes.

Com GitHub Actions, é possível automatizar desde tarefas simples até pipelines completos de CI/CD utilizando apenas arquivos YAML versionados junto ao código-fonte.

Essa abordagem aumenta a produtividade, reduz erros manuais e melhora significativamente a qualidade e velocidade das entregas de software.

---

⬅️ **Anterior:** Git, GitHub & GitHub Actions