# Configuração Inicial na Wolke

Este guia cobre o setup inicial e a configuração básica para **qualquer** aplicação na Wolke.

## Pré‑requisitos

- Conta na Wolke
- Repositório Git com o projeto
- Runtime compatível com a stack (Python/Node/Go/etc.)

## Criar a Aplicação na Wolke

1. Acede ao painel: https://staging.wolke.host/apps/create
2. Cria uma nova aplicação.
3. Seleciona **Deploy via Git**.
4. Escolhe o repositório e a branch.
5. Confirma para iniciar o primeiro deploy.

## Variáveis de Ambiente e Segredos

Define as variáveis no painel da Wolke (Settings → Environment). Exemplos comuns:

- `DATABASE_URL`
- `REDIS_URL`
- `SENTRY_DSN`
- `APP_ENV=production`

> As variáveis específicas do framework (ex: Django) devem ser definidas no guia do respectivo framework.

## Configuração do `wolke.yaml`

Cria um ficheiro `wolke.yaml` na raiz do projeto para definir build e run. Exemplo genérico:

```yaml
name: my-app
language: python

build:
  command: |
    pip install --no-cache-dir -r requirements.txt

run:
  command: python app.py
  port: 8000
```

> Para comandos específicos (ex: Gunicorn, collectstatic), consulta o guia do teu framework.

## Deploy via Git

Cada push para a branch configurada cria um novo deploy:

```bash
git add .
git commit -m "Deploy Django"
git push origin main
```

## Comandos One‑Off

Usa o painel da Wolke para executar comandos one‑off no container (ex: migrações, seeds, tarefas de manutenção).

## Links Úteis

- Wolke: https://staging.wolke.host/apps/create
