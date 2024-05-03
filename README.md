# API-
Framework FastAPI, de alto desempenho, fácil de aprender, fácil de codificar, pronto para produção. FastAPI é um framework web moderno e rápido (alta performance) para construção de APIs com Python 3.6 ou superior, baseado nos padrões de dicas de tipo do Python.

from fastapi import FastAPI, Query, HTTPException
from fastapi_pagination import Page, paginate
from pydantic import BaseModel
from typing import Optional
from sqlalchemy.exc import IntegrityError

app = FastAPI()

# Modelo Pydantic para atletas
class Atleta(BaseModel):
    nome: str
    centro_treinamento: str
    categoria: str

# Simulação de uma lista de atletas para exemplo de paginação
fake_db = [
    {"nome": "João", "centro_treinamento": "Gym A", "categoria": "Profissional"},
    {"nome": "Ana", "centro_treinamento": "Gym B", "categoria": "Amador"}
] * 10  # Multiplica para criar mais dados

@app.get("/atletas", response_model=Page[Atleta])
async def read_atletas(nome: Optional[str] = Query(None), cpf: Optional[str] = Query(None)):
    atletas = [Atleta(**atleta) for atleta in fake_db if (nome is None or atleta['nome'] == nome) and (cpf is None)]
    return paginate(atletas)

@app.get("/atletas/{atleta_id}", response_model=Atleta)
async def get_atleta(atleta_id: int):
    try:
        # Simulação de obtenção de atleta
        atleta = Atleta(**fake_db[atleta_id])
        return atleta
    except IndexError:
        raise HTTPException(status_code=404, detail="Atleta não encontrado")

@app.post("/atletas", response_model=Atleta)
async def create_atleta(atleta: Atleta):
    try:
        # Simulação de tentativa de inserção de atleta
        fake_db.append(atleta.dict())
        return atleta
    except IntegrityError:
        raise HTTPException(status_code=303, detail=f"Já existe um atleta cadastrado com o cpf: {atleta.dict().get('cpf')}")

# Comandos de execução e outros procedimentos seriam adicionados conforme necessário
