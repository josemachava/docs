# Troubleshooting

## Build falha com erro de dependências

- Confirma a versão do Python suportada no projeto.
- Verifica se `requirements.txt` tem versões compatíveis.

## Erro `DisallowedHost`

- Ajusta `ALLOWED_HOSTS` e `CSRF_TRUSTED_ORIGINS` nas variáveis de ambiente.

## `SECRET_KEY` ausente

- Garante que a variável `SECRET_KEY` está definida no wolke.

## `collectstatic` falha

- Garante `STATIC_ROOT` definido no `settings.py`.
- Confirma que `whitenoise` está instalado.

## 404 em ficheiros estáticos

- Verifica se `WhiteNoise` está no `MIDDLEWARE`.
- Confirma se `collectstatic` correu no build.

## Erro 502 / aplicação não inicia

- Confirma o comando do `gunicorn` definido no painel do wolke.
- Verifica o nome correto do módulo WSGI.

## Migrações não aplicadas

- Confirma que o **Pre‑Deploy Command** inclui `python manage.py migrate` e faz novo deploy.

## Erros de base de dados

- Valida o `DATABASE_URL`.
- Confirma acesso à base de dados (porta, credenciais).

## Cache não funciona

- Confirma `REDIS_URL` e driver correto em `CACHES`.

## Celery não inicia

- Garante que o worker aponta para o módulo correto.
- Confirma o broker (`REDIS_URL` ou RabbitMQ).

## Logs insuficientes

- Ativa logging para `django` e `gunicorn`.
- Verifica logs de build e runtime no painel.
