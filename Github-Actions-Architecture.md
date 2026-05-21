# 🏗️ Arquitetura e Funcionamento do GitHub Actions

## 📘 Introdução

Compreender a arquitetura interna do GitHub Actions é fundamental para otimizar workflows e tomar decisões técnicas mais assertivas.

Esta seção explora como os workflows são processados, os tipos de runners disponíveis, os sistemas operacionais suportados e as limitações da plataforma.

O GitHub Actions combina automação moderna, execução distribuída e escalabilidade automática para oferecer uma experiência eficiente de CI/CD integrada ao GitHub.

---

# 🧩 Definição

A arquitetura do GitHub Actions é baseada em um sistema distribuído de processamento de workflows.

Eventos disparam execuções automaticamente, que são enfileiradas e distribuídas para runners disponíveis. O sistema gerencia recursos, escalabilidade e isolamento de forma transparente ao usuário.

---

# ⚙️ Explicação Técnica

# 🔄 1. Como os Workflows são Processados

O processamento de workflows segue um ciclo estruturado composto por várias etapas.

## 📌 Fluxo de Execução

```text
Trigger Event
      ↓
Workflow Parser
      ↓
Job Queue
      ↓
Runner Allocation
      ↓
Environment Setup
      ↓
Step Execution
      ↓
Cleanup
      ↓
Results Storage
```

## 📝 Etapas do processamento

| Etapa | Descrição |
|---|---|
| 🚀 Trigger Event | Evento que dispara o workflow |
| 📄 Workflow Parser | Interpreta o arquivo YAML |
| 📦 Job Queue | Enfileira os jobs |
| 🖥️ Runner Allocation | Seleciona o runner disponível |
| ⚙️ Environment Setup | Configura ambiente e dependências |
| ▶️ Step Execution | Executa os steps sequencialmente |
| 🧹 Cleanup | Remove arquivos temporários |
| 📊 Results Storage | Armazena logs e artifacts |

---

## ⚡ Execução Paralela

O GitHub Actions permite execução paralela automaticamente.

### 📌 Características

- 🧱 Jobs independentes executam em paralelo
- 🔗 Jobs com `needs` executam sequencialmente
- 🧪 Matrix jobs criam múltiplas execuções simultâneas
- 🚀 Aumenta velocidade e cobertura de testes

---

# 🖥️ 2. Runners Hospedados vs Self-hosted

Os runners são responsáveis por executar workflows.

Existem dois modelos principais:

- ☁️ GitHub-hosted
- 🏠 Self-hosted

---

## ☁️ GitHub-hosted Runners

São máquinas virtuais totalmente gerenciadas pelo GitHub.

### ✅ Características

- Ambiente limpo a cada execução
- Isolamento automático
- Sem necessidade de manutenção
- Ferramentas pré-instaladas
- Provisionamento automático

### 📊 Recursos padrão

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| Memória | 7 GB RAM |
| Armazenamento | 14 GB SSD |

---

## 🏠 Self-hosted Runners

São runners configurados e mantidos pelo próprio usuário.

### ✅ Características

- Controle total do ambiente
- Hardware customizável
- Instalação de softwares específicos
- Pode rodar localmente ou em nuvem

### ⚠️ Responsabilidades

- Segurança
- Atualizações
- Manutenção
- Monitoramento

---

## ⚖️ Comparação

| Característica | GitHub-hosted | Self-hosted |
|---|---|---|
| 🛠️ Gerenciamento | GitHub | Usuário |
| 🔒 Isolamento | Automático | Manual |
| ⚙️ Customização | Limitada | Total |
| 💰 Custo | Minutos cobrados | Infraestrutura própria |
| 🚀 Facilidade | Alta | Média/Alta |
| 🧠 Controle | Baixo | Total |

---

# 💻 3. Sistemas Operacionais Disponíveis

O GitHub Actions suporta múltiplos sistemas operacionais.

---

## 🐧 Ubuntu (Linux)

O ambiente mais popular para workflows CI/CD.

### 📌 Versões disponíveis

- `ubuntu-latest`
- `ubuntu-22.04`
- `ubuntu-20.04`

### 🛠️ Ferramentas pré-instaladas

- Docker
- Node.js
- Python
- Java
- Maven
- Gradle
- Git

---

## 🪟 Windows

Ideal para aplicações .NET e ambientes Microsoft.

### 📌 Versões disponíveis

- `windows-latest`
- `windows-2022`
- `windows-2019`

### 🛠️ Ferramentas incluídas

- PowerShell
- .NET SDK
- Git
- Python
- Node.js
- Chocolatey

---

## 🍎 macOS

Necessário para builds Apple/iOS.

### 📌 Versões disponíveis

- `macos-latest`
- `macos-12`
- `macos-11`

### 🛠️ Ferramentas incluídas

- Xcode
- Swift
- Homebrew
- Ferramentas Apple

---

# 📊 4. Limites e Cotas da Plataforma

O GitHub Actions possui limites dependendo do tipo de repositório e plano da conta.

## 📋 Tabela de Limites

| Recurso | Público | Privado |
|---|---|---|
| ⏱️ Minutos/mês | Ilimitado | 2.000 (Free) / 3.000 (Pro) |
| 💾 Armazenamento | 500 MB | 500 MB / 1 GB |
| 🔄 Jobs Concurrentes | 20 | 5 (Free) / 15 (Pro) |
| ⌛ Timeout por Job | 360 min | 360 min |
| 🚀 Workflow Execution | 1.000/hora | 1.000/hora |
| 🌐 API Requests | 1.000/hora | 1.000/hora |

---

## 💡 Otimizações da Plataforma

O sistema implementa diversas otimizações automáticas.

### ✅ Principais recursos

- 📦 Cache inteligente de dependências
- ⚡ Verificações rápidas antes do build
- 🧹 Cleanup automático
- 🚀 Execução paralela
- 📊 Compressão de artifacts

---

# 🧪 Exemplo Prático

Na prática, a arquitetura funciona da seguinte forma:

1. 👨‍💻 Um desenvolvedor faz push para a branch `main`
2. 🚀 O GitHub detecta o evento automaticamente
3. 📄 O arquivo YAML é interpretado
4. 📦 Os jobs são adicionados à fila
5. 🖥️ Um runner disponível é selecionado
6. ⚙️ O ambiente é configurado
7. ▶️ Os steps são executados
8. 📊 Logs e artifacts são armazenados

---

## ⚡ Execução Paralela na Prática

### 📌 Cenários possíveis

- 🧱 Jobs independentes executam simultaneamente
- 🔗 Jobs com `needs` aguardam predecessores
- 🧪 Matrix strategy cria múltiplas instâncias paralelas

Exemplo:

```yaml
strategy:
  matrix:
    java-version: [11, 17, 21]
```

Neste caso, o mesmo job será executado simultaneamente em três versões diferentes do Java.

---

# 🎯 Output Esperado

A execução dos workflows fornece diversas informações úteis.

## 📊 Informações disponíveis

| Categoria | Informações |
|---|---|
| 🖥️ Sistema | OS, arquitetura e recursos |
| ⚙️ Runner | Tipo e especificações |
| 📈 Performance | Tempo de execução e throughput |
| 📦 Artefatos | Logs e arquivos gerados |

---

# 🏁 Conclusão

A arquitetura do GitHub Actions combina simplicidade na interface com robustez na infraestrutura.

Seu sistema distribuído garante escalabilidade automática, enquanto os diferentes tipos de runners oferecem equilíbrio entre conveniência e controle total.

Compreender esses fundamentos permite:

- 🚀 Construir workflows mais eficientes
- 💰 Otimizar custos
- ⚡ Melhorar performance
- 🧠 Escolher runners adequados
- 📈 Escalar pipelines corretamente

A plataforma incentiva boas práticas de automação, resultando em workflows mais sustentáveis, rápidos e confiáveis.