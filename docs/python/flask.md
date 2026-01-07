# Deploy de uma Aplicação Flask na Wolke

Este guia explica como fazer deploy de uma aplicação **Flask** no wolke usando **deploy via Git**.

## Requisitos

- Python 3.10+
- Flask
- Gunicorn
- Repositório Git

## Estrutura do Projecto

```
.
├── app.py
└── requirements.txt
```

## Exemplo

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Hello from Wolke!"
```

## requirements.txt

```
flask
gunicorn
```



Enviar Código para Git
```
git init
git add .
git commit -m "Initial Flask app"
git remote add origin <repo-url>
git push -u origin main

```

Criar o Projecto no Wolke

Acede ao painel do Wolke

Cria um novo projecto

Selecciona Deploy via Git

Escolhe o repositório e a branch

Confirma

O deploy inicia automaticamente.

Deploy Automático

Cada push cria um novo deploy

Deploys falhados não afectam produção

Apenas deploys bem-sucedidos entram em produção

Variáveis de Ambiente

Configurações sensíveis devem ser definidas como variáveis de ambiente:

Exemplos:

FLASK_ENV=production

SECRET_KEY

DATABASE_URL

A aplicação pode aceder assim:

import os

secret = os.getenv("SECRET_KEY")

Networking

A Wolke gere a porta automaticamente

Não definas portas fixas no código

Gunicorn já está optimizado para produção

## Logs

A Wolke disponibiliza:

Logs de build