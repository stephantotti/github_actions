# 🌍 Environments e Proteções de Implantação

## 📖 Introdução

Environments organizam implantações por estágios (como `dev`, `staging` e `prod`), segregando variáveis e segredos e aplicando proteções antes de liberar o deploy.

Usar environments corretamente ajuda a:

- evitar exposições indevidas
- controlar aprovações
- criar fluxos previsíveis de promoção
- aumentar segurança e auditoria do pipeline

---

# 🎯 Definição

Um **environment** é um alvo lógico de implantação no GitHub que pode possuir:

- variáveis
- segredos
- regras de proteção
- revisores obrigatórios
- temporizadores de espera
- URL do ambiente implantado

Ao declarar um environment em um job:

```yaml
environment: prod
```

o GitHub:

✅ libera acesso aos segredos daquele ambiente  
✅ aplica regras de proteção  
✅ pode exigir aprovação manual antes do deploy

---

# ⚙️ Explicação Técnica

## 🧩 Como Funciona

O environment é declarado diretamente no job:

```yaml
jobs:
  deploy:
    environment: prod
```

ou na forma completa:

```yaml
environment:
  name: prod
  url: https://app.exemplo.com
```

---

# 🔐 Regras de Proteção

Os environments podem possuir proteções como:

- aprovação manual
- revisores obrigatórios
- tempo de espera (wait timer)
- restrições de branch
- controle de acesso

---

## 🛑 Aprovação Manual

Quando o environment exige revisão:

1. o workflow inicia normalmente
2. o job entra em estado de espera
3. um revisor autorizado aprova
4. o deploy continua

Isso evita deploys acidentais em produção.

---

# ⏳ Wait Timer

Você também pode configurar um tempo mínimo antes do deploy:

- útil para validações
- janelas de manutenção
- cooldown entre implantações

---

# 🔑 Segregação de Segredos

Cada environment pode possuir:

- secrets próprios
- vars próprias

Exemplo:

| Environment | DATABASE_URL |
|---|---|
| dev | banco-dev |
| staging | banco-staging |
| prod | banco-prod |

O workflow usa sempre:

```yaml
${{ secrets.DATABASE_URL }}
```

mas o valor muda automaticamente conforme o environment ativo.

---

# 🌐 URL do Ambiente

O campo:

```yaml
url:
```

permite exibir um link clicável na execução do workflow.

Exemplo:

```yaml
environment:
  name: prod
  url: https://app.exemplo.com
```

Após o deploy, o GitHub mostra:

✅ link do ambiente implantado  
✅ histórico de deployments  
✅ rastreamento por ambiente

---

# 🧠 Boas Práticas

## ✅ Utilize nomes padronizados

Exemplos:

- `dev`
- `staging`
- `prod`

---

## ✅ Separe build/test de deploy

Exemplo ideal:

```text
build → test → deploy-dev → deploy-prod
```

---

## ✅ Restrinja aprovações apenas para produção

Evita lentidão desnecessária nos ambientes inferiores.

---

## ✅ Armazene segredos no environment correto

Evite colocar tudo no nível global do repositório.

---

## ✅ Revise quem pode aprovar deploys

Controle de acesso é essencial para ambientes críticos.

---

# ⚠️ Cuidados Importantes

## 🔸 Segredos só ficam disponíveis após aprovação

Se o environment exigir revisão:

- os segredos permanecem bloqueados
- o job não acessa variáveis protegidas até aprovação

---

## 🔸 Não reutilize segredos de produção em ambientes inferiores

Isso reduz risco de vazamento ou uso incorreto.

---

## 🔸 Use proteção apenas onde necessário

Proteções excessivas podem tornar pipelines lentos.

---

# 💻 Exemplo Prático

```yaml
name: Aula 20 — Environments e Proteções

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    environment:
      name: prod
      url: https://app.exemplo.com

    steps:
      - name: Preparar pacote
        run: |
          echo "Gerando artefatos de deploy..."

      - name: Usar segredo protegido
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          echo "Conectando ao banco..."
          # ./deploy.sh --db "${DATABASE_URL}"

      - name: Finalizar deploy
        run: |
          echo "Deploy concluído em ${GITHUB_REF_NAME:-branch}"
```

---

# 🔍 O Que Este Exemplo Demonstra

✅ Uso de environment no job

✅ URL de ambiente implantado

✅ Segregação de segredos por estágio

✅ Aprovação antes do deploy

✅ Uso seguro de secrets via `env`

✅ Estrutura de deploy organizada

---

# 🔄 Fluxo de Execução

```text
Workflow iniciado
        ↓
Job entra no environment "prod"
        ↓
GitHub solicita aprovação
        ↓
Revisor aprova
        ↓
Secrets do environment são liberados
        ↓
Deploy executa
        ↓
URL do ambiente é exibida
```

---

# 🔗 Link Oficial

📘 Environments e Proteções:  
https://docs.github.com/pt/actions/deployment/targeting-different-environments/managing-environments-for-deployment

---

# ✅ Conclusão

Environments fornecem uma camada poderosa de organização e segurança para pipelines de implantação.

Com eles, é possível:

- separar configurações por estágio
- controlar acesso a segredos
- exigir aprovações manuais
- rastrear deployments
- exibir URLs de ambientes implantados

Ao combinar environments com boas práticas de permissões e segregação de segredos, seus workflows se tornam mais seguros, auditáveis e previsíveis em ambientes reais de CI/CD.

---