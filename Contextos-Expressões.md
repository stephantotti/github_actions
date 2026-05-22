# 📚 Contextos e Expressões no GitHub Actions (Fundamentos)

## 📖 Introdução

Antes de criar condições, reutilizar saídas de steps ou montar estratégias de execução, é essencial entender dois pilares do GitHub Actions: **contextos** e **expressões**. Eles determinam “o que está disponível” e “como calcular” valores no YAML, permitindo escrever workflows mais claros, seguros e previsíveis.

---

# 🎯 Definição

## 🔹 Contextos

Contextos são estruturas de dados expostas pelo GitHub Actions durante a execução do workflow, como:

- `github`
- `env`
- `vars`
- `secrets`
- `runner`
- `steps`
- `needs`
- `matrix`

Eles representam **“o que está disponível”** para consulta e decisão no pipeline.

---

## 🔹 Expressões

Expressões são trechos avaliados dentro de:

```yaml
${{ ... }}
```

Elas acessam contextos, aplicam operadores e chamam funções como:

- `hashFiles()`
- `toJSON()`
- `fromJSON()`

São utilizadas em campos como:

- `if:`
- `env:`
- `with:`
- strings interpoladas no YAML

---

# ⚙️ Explicação Técnica

## 🧠 Uso de Contextos

Os contextos permitem:

- Criar condições
- Parametrizar actions
- Compartilhar outputs
- Montar nomes dinâmicos
- Ler metadados do workflow

### Exemplo

```yaml
if: ${{ github.event_name == 'push' }}
```

---

# 🧩 Principais Contextos

## 🔹 github

Contém informações sobre:

- repositório
- branch
- commit SHA
- usuário
- tipo de evento

### Exemplo

```yaml
${{ github.repository }}
${{ github.ref }}
${{ github.actor }}
```

---

## 🔹 env

Representa variáveis de ambiente definidas no:

- workflow
- job
- step

### Exemplo

```yaml
env:
  APP_ENV: dev
```

Uso no shell:

```bash
echo "$APP_ENV"
```

---

## 🔹 vars

Variáveis gerenciadas pelo GitHub via:

- organização
- repositório
- environment

Indicadas para valores **não sensíveis**.

### Exemplo

```yaml
${{ vars.APP_ENV }}
```

---

## 🔹 secrets

Armazena informações confidenciais.

- mascaradas automaticamente
- não devem ser impressas em logs
- usadas via `env:` ou `with:`

### Exemplo

```yaml
env:
  TOKEN: ${{ secrets.API_TOKEN }}
```

---

## 🔹 runner

Informações sobre o executor do workflow:

- sistema operacional
- arquitetura

### Exemplo

```yaml
${{ runner.os }}
```

---

## 🔹 steps

Permite acessar outputs de steps anteriores.

### Exemplo

```yaml
${{ steps.build.outputs.version }}
```

⚠️ O step precisa possuir `id`.

---

## 🔹 needs

Permite acessar outputs de outros jobs.

### Exemplo

```yaml
${{ needs.build.outputs.image }}
```

---

## 🔹 matrix

Representa os valores ativos do `strategy.matrix`.

### Exemplo

```yaml
${{ matrix.node }}
```

---

# 🧮 Expressões

As expressões utilizam a sintaxe:

```yaml
${{ expressão }}
```

---

## 🔹 Operadores Comuns

| Operador | Função |
|---|---|
| `==` | Igual |
| `!=` | Diferente |
| `&&` | E lógico |
| `||` | Ou lógico |

---

## 🔹 Funções Úteis

### `hashFiles()`

Gera hash determinístico de arquivos.

```yaml
${{ hashFiles('**/package-lock.json') }}
```

Muito usado em cache.

---

### `toJSON()`

Converte contexto em JSON.

```yaml
${{ toJSON(matrix) }}
```

Útil para debug.

⚠️ Não usar com segredos.

---

### `fromJSON()`

Converte string JSON em objeto novamente.

```yaml
${{ fromJSON(vars.CONFIG) }}
```

---

# 🛡️ Boas Práticas

✅ Use condições explícitas:

```yaml
if: github.ref == 'refs/heads/main'
```

✅ Dê nomes claros aos steps:

```yaml
id: build
```

✅ Use `toJSON()` apenas com dados não sensíveis.

✅ Nunca imprima secrets em logs.

✅ Utilize `env:` para passar segredos ao shell.

---

# ⚠️ Limitações Importantes

Nem todos os contextos funcionam em todos os lugares do YAML.

Exemplos:

- `secrets` não pode ser usado em `runs-on`
- `matrix` só existe dentro do job com `strategy.matrix`
- `steps` depende de `id`

Erros de nome geram valores vazios silenciosamente.

---

# 💻 Exemplo Prático

```yaml
name: Aula 17 — Contextos e Expressões

on:
  workflow_dispatch:

jobs:
  info:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        os: [ubuntu-latest]
        node: [18]

    steps:
      - name: Exibir metadados do evento
        run: |
          echo "Evento: ${{ github.event_name }}"
          echo "Branch: ${{ github.ref }}"
          echo "Actor: ${{ github.actor }}"
          echo "Runner OS: ${{ runner.os }}"

      - name: Definir variável usando vars
        env:
          APP_ENV: ${{ vars.APP_ENV }}
        run: |
          echo "APP_ENV=${APP_ENV:-dev}"

      - id: prepara
        name: Gerar output
        run: |
          echo "value=ok" >> "$GITHUB_OUTPUT"

      - name: Consumir output do step
        if: ${{ steps.prepara.outputs.value == 'ok' }}
        run: |
          echo "Output recebido com sucesso"

      - name: Mostrar matrix em JSON
        run: |
          echo '${{ toJSON(matrix) }}'
```

---

# 🔍 O Que Este Exemplo Demonstra

✅ Uso do contexto `github`

✅ Uso do contexto `runner`

✅ Uso de `vars`

✅ Produção de outputs com `GITHUB_OUTPUT`

✅ Consumo de outputs via `steps.<id>.outputs`

✅ Uso de condicionais com `if:`

✅ Debug seguro usando `toJSON(matrix)`

---

# 🔗 Links Oficiais

- 📘 Contextos:  
  https://docs.github.com/pt/actions/learn-github-actions/contexts

- 📘 Expressões:  
  https://docs.github.com/pt/actions/learn-github-actions/expressions

---

# ✅ Conclusão

Contextos revelam **“o que”** está disponível em cada ponto do workflow, enquanto expressões determinam **“como”** combinar e avaliar essas informações.

Dominar ambos permite:

- criar condicionais inteligentes
- compartilhar dados entre steps e jobs
- montar workflows dinâmicos
- depurar pipelines rapidamente

Sempre valide a disponibilidade dos contextos em cada campo do YAML, trate segredos com rigor e prefira expressões simples e explícitas para manter seus workflows previsíveis, seguros e fáceis de manter.

---