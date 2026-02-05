# Deploy de Aplicações Flask no Wolke

## Overview

Este guia descreve como fazer deploy de aplicações **Flask** no [**wolke**](https://wolke.host), incluindo configuração de ambiente, base de dados, estáticos, Gunicorn, migrações e boas práticas de produção.

## Sobre Flask

O Flask é um micro-framework web em Python, conhecido pela sua simplicidade e flexibilidade. Ao contrário do Django, ele fornece o essencial, permitindo que o desenvolvedor adicione extensões conforme a necessidade.

## Pré‑requisitos

- Python 3.10+
- Flask
- Gunicorn
- Git
- Conta Wolke

## Configuração Inicial no Wolke

Para passos comuns a qualquer framework, consulta [Wolke Setup](../wolkeSetup.md). O que é específico do Flask está detalhado abaixo.

## Estruturas Recomendadas

É fundamental seguir rigorosamente uma destas estruturas para garantir que o processo de build e deploy no **wolke** ocorra sem falhas. A organização correta dos ficheiros permite que o servidor Gunicorn localize o ponto de entrada da aplicação e que o Flask consiga servir os recursos necessários automaticamente.

### Simples

Ideal para APIs ou aplicações minimalistas:

```.
├── app.py
└── requirements.txt
```

#### Exemplo de Aplicação

`app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Hello from Wolke!"

@app.route("/healthz")
def health():
    return {"status": "healthy"}, 200
```

`requirements.txt`

```bash
flask
gunicorn
```

### Com Estáticos e Templates

Ideal para aplicações web completas com interface:

```
.
├── app.py
├── static/
│ ├── css/
│ └── js/
├── templates/
│ └── index.html
└── requirements.txt
```

#### Exemplo de Aplicação

`app.py`

```python
import os
from flask import Flask, render_template

app = Flask(__name__,
    static_folder='static',
    template_folder='templates')

@app.route("/")
def index():
    # Retorna o ficheiro HTML da pasta /templates
    return render_template("index.html")

@app.route("/healthz")
def health():
    return {"status": "healthy"}, 200

if __name__ == "__main__":
    app.run()
```

`requirements.txt`

```text
flask
gunicorn
psycopg2-binary
python-dotenv
```

##### Definição do template

Cria uma pasta chamada `templates` e guarda lá este ficheiro como `index.html`. Este exemplo já inclui a ligação para o CSS (estáticos):
`templates/index.html`

```html
<!DOCTYPE html>
<html lang="pt-mz">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Flask no Wolke</title>
    <link
      rel="stylesheet"
      href="{{ url_for('static', filename='css/style.css') }}"
    />
  </head>
  <body>
    <div class="container">
      <h1>Olá de Mundo!</h1>
      <p>
        A tua aplicação Flask está a correr de forma <strong>smooth</strong> no
        Wolke.
      </p>
      <img src="{{ url_for('static', filename='img/logo.png') }}" alt="Logo" />
    </div>
  </body>
</html>
```

##### Definição de estilos

Cria a pasta `static/css/` e adiciona este estilo básico para testar se os estáticos estão a carregar:

`static/css/style.css`

```css
body {
  background-color: #f4f4f9;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
}

.container {
  text-align: center;
  background: white;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #333;
}
```

### Enviar Código para GitHub

```
git init
git add .
git commit -m "Initial Flask app"
git remote add origin <repo-url>
git push -u origin main

```

## Configuração para Produção

No Flask as configurações são carregadas via objeto ou variáveis de ambiente no próprio app.py ou num ficheiro `config.py`:

`config.py`

```Python
import os

class Config:
    SECRET_KEY = os.getenv("SECRET_KEY")
    DEBUG = os.getenv("DEBUG", "False") == "True"
    SQLALCHEMY_DATABASE_URI = os.getenv("DATABASE_URL")
```

### Carregue as configuraçãoes no `app.py`

`app.py`

```python
# os seus outros imports

from config import Config
app.config.from_object(Config)

# outra parte da aplicação

```

## Base de Dados e Migrações

O Wolke utiliza o conceito de **Environment Variables** (Variáveis de Ambiente) para gerir credenciais sensíveis e o **Pre-Deploy Command** para preparar o ambiente antes da aplicação entrar em funcionamento.

### Configuração da Base de Dados (PostgreSQL)

Para ligar a sua aplicação a uma base de dados, não deve colocar as credenciais diretamente no código. Em vez disso:

- Define `DATABASE_URL` (ex: `postgres://user:pass@host:5432/dbname`).

### Gestão de Migrações

As migrações garantem que a estrutura da sua base de dados (tabelas e colunas) está sincronizada com o seu código Python.

#### Pré‑Deploy Command

Para automatizar este processo e evitar erros manuais, o Wolke permite executar comandos antes do deploy final. Se estiver a utilizar a extensão **Flask-Migrate**, adicione o seguinte comando no campo **Pre‑Deploy Command**:

```bash
flask db upgrade
```

## Ficheiros Estáticos

Para servir ficheiros da pasta `/static` de forma eficiente:

- O Flask serve automaticamente ficheiros em `/static`.

## Media Files

- Em produção, recomenda‑se armazenamento externo (S3/MinIO).
- O sistema de ficheiros dos containers é efémero e os ficheiros serão perdidos no restart.

## Troubleshooting

Consulta [Troubleshooting](../troubleshooting.md) para erros comuns.

## Links Úteis

- Wolke: [https://wolke.host/apps/create](https://wolke.host/apps/create)
