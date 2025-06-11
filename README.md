# Guia de Testes de Integração no DevOps 🚧

## 1. Introdução aos Testes de Integração
Testes de integração validam a interação entre módulos do sistema, como frontend, backend e banco de dados. No contexto DevOps, esses testes garantem que os componentes funcionam bem em conjunto, evitando erros durante a entrega contínua.

**Objetivos principais:**
- Garantir que rotas backend estejam conectadas corretamente com o banco de dados.
- Validar comunicação entre o frontend e a API.
- Simular cenários reais com múltiplos componentes.
- Reduzir falhas na entrega para produção.

## 2. Diferença entre Teste Unitário e de Integração

| Tipo de Teste | Escopo | Dependências simuladas? | Ferramentas |
|---------------|--------|---------------------------|-------------|
| Unitário       | Função/método isolado | Simuladas com mocks     | pytest, Jest |
| Integração   | Vários módulos reais     | Parciais ou reais       | pytest + banco real, Selenium |

## 3. Quando Planejar Testes de Integração?

Seguindo a proposta de *Entrega Contínua* de Martin Fowler:
- Testes unitários são criados durante o desenvolvimento de cada feature (TDD ou logo após).
- Testes de integração devem ser pensados **após a finalização funcional** da feature, pois dependem da existência de módulos interagindo.
- Para cada Sprint, os testes de integração são implementados após o merge da task para a branch `develop`.

### Justificativa com a Pirâmide de Testes
A pirâmide de testes de Mike Cohn, reforçada por Martin Fowler, indica que a maior parte dos testes deve ser unitária, seguida de testes de integração e por fim poucos testes E2E.
```
    🔺 Testes E2E (Selenium)
   🔹 Testes de Integração (pytest + DB)
  🔸 Testes Unitários (pytest, Jest)
```

Isso garante maior velocidade, manutenção e confiabilidade na base de testes.

## 4. Ferramentas Utilizadas
- **Backend (FastAPI/Python):** `pytest`, `httpx`, banco PostgreSQL local ou Docker.
- **Frontend (React/TSX):** `Selenium`, controlando o navegador para simular uso real.
- **Banco de Dados:** PostgreSQL com migrations aplicadas.
- **CI/CD:** GitHub Actions

## 5. Execução Automática com GitHub Actions
**Workflow:**
- Trigger: `push` ou `pull_request` para `develop`
- Jobs:
  - `setup-backend`: Sobe banco real via Docker
  - `run-pytest-integration`: Executa testes com banco
  - `run-selenium`: Roda testes E2E no frontend via navegador headless

## 6. Estrutura Recomendada dos Testes
/tests
|__ integration
|__ test_backend_api.py
|__ test_frontend_flow.py


- **test_backend_api.py:** Testa chamadas reais para a API com dados inseridos no banco.
- **test_frontend_flow.py:** Testa navegação no app via Selenium, simulando fluxo do usuário.

## 7. Regras de Skip em Testes ⚠️🚪

Durante o desenvolvimento ou execução no CI, nem todos os testes devem ser executados sempre. O `pytest` oferece formas de controlar isso:

- `@pytest.mark.skip`: pula o teste sempre.
- `@pytest.mark.skipif`: pula o teste baseado em uma condição.
- `@pytest.mark.xfail`: espera que o teste falhe (por exemplo, por bug conhecido).

### Exemplos:
```python
import os
import pytest

API_URL = os.getenv("API_TERCEIRA_PARTE")

@pytest.mark.skipif(API_URL is None, reason="API externa não configurada")
def test_chamada_api():
    response = requests.get(f"{API_URL}/dados")
    assert response.status_code == 200

@pytest.mark.skip(reason="Implementação ainda não concluída")
def test_editar_estacao():
    ...

@pytest.mark.xfail(reason="Bug conhecido na validação do CEP")
def test_criar_estacao_sem_nome():
    ...
```
###
Boas Práticas:
Sempre usar reason="..." para justificar o skip.

Revisar testes com skip a cada sprint.

Não usar skip para esconder bugs reais.

Usar skipif para ambientes específicos (ex: banco indisponível no CI).

8. Checklist para Testes de Integração ✅
 Banco de dados real/Docker configurado

 API respondendo corretamente (status 200/201/422/404)

 Frontend realizando chamadas válidas à API

 Selenium testando fluxo completo do usuário

 CI/CD automatizando execução dos testes em cada push

 Uso de mocks apenas para serviços externos, nunca para banco

 Todos os testes @skip ou @xfail têm justificativa

 Testes refletem cenários reais e edge cases

 Cobertura mínima de 80% garantida no pipeline

 Testes falhos impedem merge em main

9. Exemplo de Fluxo Integração na Sprint
Dev finaliza a task e faz merge para develop.

O pipeline de integração é executado automaticamente.

Caso falhe, o merge para main é bloqueado.

A cobertura dos testes é revisada.

No final da sprint, os testes são reexecutados antes do deploy.

✅ Resultado Esperado:
Redução de bugs na integração frontend-backend.

Testes replicáveis no ambiente de CI.

Validação de integração com banco e outros serviços.


Pirâmide de Testes — Estação Meteorológica
Visão Geral
Baseado nos 36 User Stories e critérios de aceitação do sistema, a estratégia de testes segue o modelo de Pirâmide de Testes proposta por Mike Cohn e Martin Fowler, garantindo:

Alta cobertura de testes unitários.

Testes de integração focados nas comunicações principais.

Testes E2E cobrindo os fluxos críticos de usuário.

🎯 Distribuição dos testes
```
                🔺 Testes End-to-End (E2E)
            🔹 Testes de Integração
        🔸 Testes Unitários
```

🔸 Testes Unitários (Base da pirâmide — ~70%)
Responsáveis por garantir que cada módulo e função isolada funcione corretamente.

Cobrem:

Validações de entrada (ex.: US01, US15, US17, US09)

Regras de negócio (ex.: US25 - condições de alerta)

Conversão de dados (ex.: US20 - -50°C a 60°C)

Autenticação e permissões (ex.: US36)

Lógica de cálculos estatísticos (ex.: US34)

Formatação de dados exportados (ex.: US23)

Exemplos:

User Story	Exemplo de Teste Unitário
US01	Validação de campos obrigatórios na criação de estação
US15	Verificação de e-mail único ao criar usuário
US25	Validação da regra de condição de alerta

🔹 Testes de Integração (Meio da pirâmide — ~20%)
Responsáveis por validar a comunicação entre módulos e componentes.

Cobrem:

CRUD completo das entidades principais (US01–US18)

Integração API ↔ Banco de Dados (PostgreSQL)

Integração IoT (US19–US21, US27–US29)

Envio de notificações (US24, US26)

Upload e armazenamento de dados coletados

Autenticação e permissões reais

Exemplos:

User Story	Exemplo de Teste de Integração
US21	Inserção de dados processados com integridade
US26	Envio de notificações via e-mail/SMS
US29	Envio de dados do datalogger via rede

🔺 Testes End-to-End (Topo da pirâmide — ~10%)
Responsáveis por validar os fluxos completos como o usuário final irá utilizar.

Cobrem:

Login e acesso ao sistema (US36)

Fluxo completo de cadastro e manutenção de estações (US01–US05)

Cadastro e manutenção de usuários, parâmetros e alertas (US06–US18)

Visualização do dashboard e dados históricos (US22–US23)

Execução dos alertas (US24–US26)

Fluxo de coleta, processamento e exibição de dados IoT (US19–US21)

Exemplos de Fluxos E2E:

Fluxo Completo	User Stories Relacionadas
Cadastro de estação	US01, US02, US03, US04, US05
Cadastro de usuários	US15, US16, US17, US18
Visualização de dados	US22, US23, US24
Geração de alertas	US11, US12, US13, US14, US25
Recebimento e processamento IoT	US19, US20, US21, US27–US29

Ferramentas sugeridas para E2E:
Selenium, Cypress, Playwright

🛡 Testes complementares (fora da pirâmide)
Tipo | User Stories Relacionadas | Ferramentas Sugeridas
Performance	US22 (Dashboard), US29 (Envio de dados)	JMeter, k6
Segurança	US36 (Login e acesso)	OWASP ZAP
Carga	US19–US21 (IoT alta frequência)	Locust
Usabilidade	US33–US35 (Guias educativos)	Testes manuais

✅ Benefícios da pirâmide aplicada
Cobertura ampla com menos esforço em E2E.

Detecção rápida de falhas via testes unitários.

Validação real de fluxos de integração antes do deploy.

Confiança na qualidade do sistema.
