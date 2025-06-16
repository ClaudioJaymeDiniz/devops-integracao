# Guia Operacional de Execução para o Dev Tester

## 1️⃣ Objetivo do Guia

Este guia fornece o passo a passo para que o Dev Tester execute, monitore e valide os testes de integração no pipeline de CI/CD, seguindo as práticas de Continuous Delivery (Martin Fowler) aplicadas no projeto.

---

## 2️⃣ Antes de Executar

- ✅ Validar que as branches de feature/task foram mergeadas em `develop`.
- ✅ Garantir que o pipeline de testes unitários já passou com sucesso.
- ✅ Validar a cobertura mínima de 80% nos unitários.

---

## 3️⃣ Execução dos Testes de Integração

### Ambiente de Testes

- Backend: FastAPI com PostgreSQL (Docker)
- Frontend: React TypeScript (Jest/Selenium)
- Banco de dados populado com dados de fixtures

### Pipeline Automático

- 🚀 Disparo automático ao realizar `push` ou `pull request` na branch `develop`.
- 🔄 O GitHub Actions executará:
  - `pytest tests/integration/`
  - `httpx` para simular chamadas REST reais
  - Selenium (opcional para E2E)

### Execução Local (opcional de validação manual)

```bash
# Backend
docker-compose up -d  # Sobe banco local
pytest tests/integration/ --cov=app

# Frontend
npm run test:integration
```

### 4️⃣ Em Caso de Falha
🔎 Identificar logs no GitHub Actions

🐛 Validar se a falha é:

De lógica (ex.: validação mal implementada)

De ambiente (ex.: variável ausente)

Intermitente (ex.: timeout, dependência externa)

⚠ Se o erro for flake (instável), investigar causa raiz. Jamais usar @pytest.mark.skip como solução inicial.

### 5️⃣ Uso Controlado de Skips
✅ Permitido apenas via @pytest.mark.skipif com:

Motivo técnico documentado

Acompanhado de uma Issue no Jira

Revisão obrigatória pelo líder de DevOps

❌ Nunca utilizar skip por simples conveniência de falha.

### 6️⃣ Quando criar novos Testes de Integração
Para cada nova User Story (US) entregue.

Sempre que houver integração entre:

Banco de Dados ↔ API

Frontend ↔ Backend

Serviços externos ↔ API

Exemplo:

Nova rota POST /estacao/: criar caso de teste com dados válidos e inválidos.

Nova consulta GET /dashboard: mockar dados e validar atualização.

### 7️⃣ Validação Final
✅ Todos testes de integração passaram

✅ Cobertura de Integração ≥ 80%

✅ Nenhum skip indevido

✅ Evidência gerada no log do pipeline

✅ Pronto para merge em main

## 8️⃣ Práticas DevOps Aplicadas
🔄 Continuous Delivery (Martin Fowler):

Integração contínua validada a cada commit.

Deploy somente após integração estável.

🔺 Pirâmide de Testes aplicada:

Unitários → Integração → E2E

🔧 Automação máxima no GitHub Actions

🔐 Qualidade garantida antes de produção

