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

    🔺 Testes E2E (Selenium)
   🔹 Testes de Integração (pytest + DB)
  🔸 Testes Unitários (pytest, Jest)

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
