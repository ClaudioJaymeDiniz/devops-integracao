### Plano de Testes de Integração DevOps com Continuous Delivery

### 1. Contexto do Projeto

Backend: FastAPI com PostgreSQL

Frontend: React TypeScript

Testes unitários: pytest (backend), Jest (frontend)

CI/CD: GitHub Actions com deploy automático

Branching: Gitflow (develop -> main)

Entregas: 3 ciclos de 20 dias cada

Responsável: Dev Tester

### 2. Visão da Pirâmide de Testes

        🔺 Testes E2E (Selenium, Playwright)
       🔹 Testes de Integração (pytest, httpx, banco real)
      🔸 Testes Unitários (pytest, Jest)

   

Unitários: garantem lógica isolada.

Integração: garantem comunicação entre módulos.

E2E: garantem fluxos completos do ponto de vista do usuário.

Referência: Martin Fowler - Continuous Delivery & Test Pyramid.

### 3. Objetivos dos Testes de Integração

Validar comunicação Backend ↔ Banco

Validar comunicação Frontend ↔ Backend

Validar consistência de dados reais

Garantir que os módulos interajam corretamente

Bloquear merge caso falhem no pipeline

### 4. Como os Testes de Integração se encaixam no DevOps

Rodam após os testes unitários.

Executados sempre no merge para develop.

Dev Tester supervisiona execução no pipeline.

Corrigir falhas antes de prosseguir para main.

### 5. Práticas de Continuous Delivery

Testes sempre rodando automaticamente no CI.

Feedback rápido para os devs.

Pipeline bloqueia deploy em caso de falha.

Ambientes isolados para execução dos testes.

Minimizar uso de skips (apenas quando controlados).

### 6. Checklists

### Checklist de Execução

- [x] Banco PostgreSQL disponível via Docker
- [x] Variáveis de ambiente carregadas (pytest dotenv)
- [x] API FastAPI levantada para testes
- [x] Dados de teste carregados via fixtures
- [x] CI disparado via GitHub Actions

### Checklist de Qualidade

- [x] Testes cobrem todos endpoints CRUD principais
- [x] Cobertura mínima 80%
- [x] Falhas identificam feature e causa
- [x] Sem uso de `skip` sem justificativa
- [x] Documentação de novos testes integrada



### 7. Exemplo de Casos de Teste de Integração

US01 - Criar Estação Meteorológica

Inserção de todos os campos obrigatórios

Validação de campos vazios (nome, CEP, lat, long)

Registro aparece na listagem após criação

US22 - Dashboard em Tempo Real

Mock de recebimento de dados recentes

Verificação de atualização a cada 5 minutos

Confirma exibição de gráficos e tabelas

US36 - Login Admin

Testa login válido

Testa login inválido

Testa sessão criada no frontend

