### 1️⃣ Dockerfile (para FastAPI)
dockerfile


```
# Usa imagem oficial do Python
FROM python:3.11

# Define diretório de trabalho
WORKDIR /app

# Copia arquivos de dependência
COPY requirements.txt .

# Instala dependências
RUN pip install --no-cache-dir -r requirements.txt

# Copia o código fonte
COPY . .

# Expõe a porta usada pelo Uvicorn
EXPOSE 8000

# Comando padrão
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 2️⃣ Exemplo de requirements.txt
```
fastapi
uvicorn[standard]
sqlalchemy
asyncpg
alembic
pydantic
python-dotenv
pytest
httpx
pytest-asyncio
pytest-cov


```
### 3️⃣ Exemplo de .env (usado localmente e também no GitHub Actions)
```
DATABASE_URL=postgresql://myuser:mypassword@db:5432/mydb

```
### 4️⃣ pytest.ini (melhora os testes no CI/CD)
```
[pytest]
addopts = --cov=app --cov-report=term --cov-report=xml
env_files =
.env
```

### 5️⃣ Ajuste no código FastAPI (exemplo do main.py)
```
import os
from fastapi import FastAPI
from dotenv import load_dotenv

# Carrega variáveis de ambiente

load_dotenv()

DATABASE_URL = os.getenv("DATABASE_URL")

app = FastAPI()

@app.get("/")
def read_root():
return {"message": "API funcionando!"}
# Claro que o resto do app teria as rotas, modelos e o SQLAlchemy configurado.
# Agora com isso seu docker-compose.yml anterior vai rodar tudo integrado.

### 6️⃣ Integração com GitHub Actions

docker-compose simulando o ambiente real.

pytest rodando integração com o banco.

Pipeline testando tudo antes de subir.
