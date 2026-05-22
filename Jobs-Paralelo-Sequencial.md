# ⚙️ Jobs: Paralelo vs Sequencial

## 📘 Introdução

Entender como os jobs são executados no GitHub Actions é essencial para construir pipelines:

- ⚡ Mais rápidos
- 🧠 Mais organizados
- 💰 Mais eficientes
- 🔄 Mais confiáveis

Por padrão, jobs executam em paralelo. Porém, em muitos cenários é necessário controlar a ordem lógica da execução.

Esta aula apresenta:

- ⚡ Execução paralela
- 🔗 Dependências com `needs`
- 🧩 Encadeamento de jobs
- 📈 Estratégias de performance
- 🛡️ Considerações de confiabilidade

---

# 🧩 Definição

## ⚙️ O que é um Job?

Um **job** é uma unidade isolada de execução dentro de um workflow.

Cada job:

- 🖥️ Roda em seu próprio runner
- 📦 Possui ambiente independente
- 🔄 Executa seus próprios steps
- 🚫 Não compartilha estado automaticamente

---

# ⚡ Execução Paralela

Por padrão:

```text
Todos os jobs sem dependência executam em paralelo
```

Isso permite paralelização horizontal.

---

# 🔗 Execução Sequencial

Para forçar ordem de execução utiliza-se:

```yaml
needs:
```

Um job dependente só inicia quando todos os jobs necessários terminam com sucesso.

---

# ⚙️ Funcionamento Técnico

## 📌 Sem `needs`

Jobs iniciam independentemente.

```yaml
jobs:
  lint:
  testes:
  build:
```

Resultado:

```text
lint     ─┐
testes   ─┼── Executam juntos
build    ─┘
```

---

## 📌 Com `needs`

```yaml
build:
  needs: testes
```

Resultado:

```text
testes → build
```

---

# 🔗 Tipos de Dependência

# 📌 Dependência única

```yaml
needs: testes
```

---

# 📌 Dependência múltipla

```yaml
needs: [lint, testes]
```

O job aguardará TODOS finalizarem.

---

# ⚠️ Comportamento em caso de falha

Se um job requerido falhar:

```text
Jobs dependentes são marcados como skipped
```

Isso cria um mecanismo automático de proteção da pipeline.

---

# 🧠 Padrões de Arquitetura

# 🌊 Fan-out

Um job inicial libera múltiplos jobs paralelos.

---

## 📄 Exemplo visual

```text
          setup
         /  |  \
      lint test build
```

---

# 🌊 Fan-in

Múltiplos jobs convergem em um job final.

---

## 📄 Exemplo visual

```text
lint ─┐
      ├── publicar
test ─┘
```

---

# ⚙️ Exemplo Conceitual

## 📌 Pipeline típica

```text
lint + testes → build → deploy
```

---

# 📦 Compartilhamento de Artefatos

Um job só consegue consumir artefatos de outro job após:

- 📤 Upload concluído
- ✅ Finalização do job anterior

Por isso:

```yaml
needs:
```

também garante consistência de artefatos.

---

# 🧩 Matrix vs Paralelismo de Jobs

## 📌 Matrix

Paraleliza variações dentro de um único job.

---

## 📄 Exemplo

```yaml
strategy:
  matrix:
    node: [18, 20]
```

---

# 📌 Jobs Paralelos

Paralelizam fases estruturais diferentes.

---

# 📄 Exemplo

```yaml
jobs:
  lint:
  testes:
  build:
```

---

# 🧠 Ambos podem coexistir

Exemplo:

```text
Job testes
   └── Matrix Node 18/20
          ↓
Job agregador final
```

---

# ⚡ Considerações de Performance

# ✅ Benefícios da Paralelização

- ⚡ Redução do tempo total
- 🚀 Feedback mais rápido
- 🧪 Execução simultânea de validações

---

# ⚠️ Possíveis Problemas

## ❌ Jobs pequenos demais

Provisionar runners pode custar mais tempo que o ganho.

---

## ❌ Cadeias longas

```text
A → B → C → D
```

podem criar gargalos.

---

## ❌ Paralelismo excessivo

Aumenta consumo de minutos em runners hospedados.

---

# 🛡️ Considerações de Confiabilidade

# ⚠️ Dependências profundas

Quanto maior a cadeia:

```text
A → B → C → D
```

maior o impacto de uma falha inicial.

---

# ⚠️ Fan-in excessivo

Jobs finais gigantes podem virar:

```text
Single Point of Failure
```

---

# 📌 Estratégia recomendada

- ⚡ Paralelizar onde possível
- 🔗 Sequenciar apenas quando necessário
- 🧠 Equilibrar tempo x custo
- 🛡️ Reduzir acoplamento

---

# 🚫 Limitações Importantes

## ❌ Dependências circulares

Isto NÃO é permitido:

```text
A depende de B
B depende de A
```

---

## 📌 Concorrência

O número máximo de jobs paralelos depende do plano GitHub.

---

# 🧪 Exemplo Prático

## 📄 Workflow Completo

```yaml
name: Jobs Paralelo vs Sequencial

on: [push]

jobs:
  lint:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Lint
        run: echo "Lint OK"

  testes:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Testes
        run: echo "Testes OK"

  build:
    needs: [lint, testes]

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build
        run: echo "Build executado"

  publicar:
    needs: build

    runs-on: ubuntu-latest

    steps:
      - name: Publicar (simulado)
        run: echo "Publicação concluída"
```

---

# 🔍 Fluxo de Execução

## 📌 Ordem real

```text
lint ─┐
      ├── build ─── publicar
test ─┘
```

---

# ⚡ O que acontece

## 🚀 Etapa 1

`lint` e `testes` iniciam juntos.

---

## 🔗 Etapa 2

`build` aguarda ambos finalizarem.

---

## 📦 Etapa 3

`publicar` só executa se `build` tiver sucesso.

---

# 🎯 Output Esperado

Ao executar o workflow:

- ⚡ Jobs independentes executam simultaneamente
- 🧪 Testes e lint reduzem tempo de CI
- 🏗️ Build ocorre apenas após validações
- 📦 Publicação ocorre somente após sucesso completo
- 🛡️ Falhas interrompem cadeia automaticamente

---

# 📌 Boas Práticas

## ✅ Recomendações importantes

- ⚡ Paralelizar validações independentes
- 🔗 Usar `needs` apenas quando necessário
- 📦 Compartilhar artefatos corretamente
- 🧩 Separar responsabilidades
- 🧠 Nomear jobs claramente
- 📊 Monitorar tempo total da pipeline

---

# ⚠️ Erros Frequentes

## ❌ Sequência desnecessária

Transforma pipeline rápida em gargalo.

---

## ❌ Jobs gigantes

Dificultam manutenção e leitura.

---

## ❌ Fan-in excessivo

Centraliza risco em um único job final.

---

## ❌ Dependências mal planejadas

Geram pipelines lentas e frágeis.

---

# 🏁 Conclusão

Jobs executam em paralelo por padrão no GitHub Actions, permitindo pipelines rápidas e eficientes.

A utilização de:

```yaml
needs:
```

fornece um mecanismo declarativo para controlar dependências e impor ordem lógica quando necessário.

O equilíbrio correto entre:

- ⚡ Paralelização
- 🔗 Sequenciamento
- 🧠 Organização
- 🛡️ Confiabilidade

é fundamental para construir pipelines modernas, performáticas e escaláveis.

Esses conceitos servem de base para tópicos mais avançados como:

- 🧩 Matrix Strategy
- 🌊 Fan-in/Fan-out avançado
- 📦 Compartilhamento de artefatos
- 🚀 Pipelines distribuídas
- 📈 Otimização de tempo total de CI/CD