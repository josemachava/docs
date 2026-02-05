# Deploy de Aplicações Django no Wolke

## Overview

Este guia descreve como fazer deploy de aplicações **Django** no wolke, incluindo configuração de ambiente, base de dados, estáticos, media, Gunicorn, migrações e boas práticas de produção.

## About Django

Django é um framework web Python de alto nível que incentiva desenvolvimento rápido e design limpo. Inclui autenticação, ORM, admin e várias funcionalidades “batteries‑included”.

## Pré‑requisitos

- Python 3.10+
- Django 4+ (ou 5+)
- Gunicorn
- Git
- Conta wolke

## Configuração Inicial no wolke

Para passos comuns a qualquer framework, consulta [wolkeSetup.md](../wolkeSetup.md). O que é específico do Django está detalhado abaixo.

## Estrutura Recomendada do Projeto

```
.
├── manage.py
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── myapp/
│   ├── views.py
│   └── urls.py
├── requirements.txt
└── static/
```

## Exemplo de Aplicação Django

`myapp/views.py`:

```python
from django.http import JsonResponse

def healthz(request):
    return JsonResponse({"status": "ok"})
```

`myapp/urls.py`:

```python
from django.urls import path
from .views import healthz

urlpatterns = [
    path("healthz/", healthz),
]
```

`myproject/urls.py`:

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("myapp.urls")),
]
```

## requirements.txt (exemplo)

```
Django>=4.2
Gunicorn>=21.2
django-environ>=0.11
dj-database-url>=2.1
whitenoise>=6.6
psycopg[binary]>=3.1
```

## Configuração do `settings.py` para Produção

```python
import os
import dj_database_url

DEBUG = os.getenv("DEBUG", "False") == "True"
SECRET_KEY = os.getenv("SECRET_KEY")
ALLOWED_HOSTS = os.getenv("ALLOWED_HOSTS", "app.wolke.host").split(",")
CSRF_TRUSTED_ORIGINS = os.getenv("CSRF_TRUSTED_ORIGINS", "https://app.wolke.host").split(",")

DATABASES = {
    "default": dj_database_url.config(default=os.getenv("DATABASE_URL"))
}

STATIC_URL = "/static/"
STATIC_ROOT = os.path.join(BASE_DIR, "staticfiles")

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "whitenoise.middleware.WhiteNoiseMiddleware",
    # ... outros middlewares
]

STATICFILES_STORAGE = "whitenoise.storage.CompressedManifestStaticFilesStorage"

MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, "media")
```

## Variáveis de Ambiente (Django)

Define no painel do wolke:

- `DJANGO_SETTINGS_MODULE`
- `SECRET_KEY`
- `DEBUG` (usar `False` em produção)
- `ALLOWED_HOSTS` (ex: `app.wolke.host`)
- `CSRF_TRUSTED_ORIGINS` (ex: `https://app.wolke.host`)
- `DATABASE_URL`
- `REDIS_URL` (opcional, cache/Celery)

### Exemplo com `django-environ`

```python
import environ

env = environ.Env()

environ.Env.read_env()

SECRET_KEY = env("SECRET_KEY")
DEBUG = env.bool("DEBUG", default=False)
ALLOWED_HOSTS = env.list("ALLOWED_HOSTS", default=["app.wolke.host"])
CSRF_TRUSTED_ORIGINS = env.list("CSRF_TRUSTED_ORIGINS", default=["https://app.wolke.host"])
DATABASES = {
    "default": env.db("DATABASE_URL")
}
```

### Segurança (Checklist)

- `DEBUG=False`
- `ALLOWED_HOSTS` definido
- `CSRF_TRUSTED_ORIGINS` definido
- `SECRET_KEY` via variável de ambiente
- `SECURE_SSL_REDIRECT=True` (se terminações TLS forem geridas pelo wolke)
- `SESSION_COOKIE_SECURE=True` e `CSRF_COOKIE_SECURE=True`

## Base de Dados

### PostgreSQL (recomendado)

- Define `DATABASE_URL` (ex: `postgres://user:pass@host:5432/dbname`).
- Executa migrações após o deploy.

### MySQL (opcional)

- Instala driver apropriado (ex: `mysqlclient` ou `PyMySQL`).
- Define `DATABASE_URL` no formato MySQL.

## Ficheiros Estáticos

- Usa `collectstatic` no build.
- `WhiteNoise` serve estáticos sem configuração extra.

## Media Files

- Em produção, recomenda‑se armazenamento externo (S3/MinIO).
- Para persistência, configura um storage backend (ex: `django-storages`).

## Migrações

### Pré‑Deploy Command (recomendado)

Para rodar migrações automaticamente a cada deploy, adiciona este comando no campo **Pre‑Deploy Command** nas **Settings** da aplicação (ver imagens):

```bash
python manage.py migrate
```

## Health Check

Usa o endpoint `/healthz/` e configura o health check no wolke apontando para este caminho.

## Logging e Monitorização

```python
LOGGING = {
    "version": 1,
    "disable_existing_loggers": False,
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
        }
    },
    "root": {
        "handlers": ["console"],
        "level": "INFO",
    },
}
```

## Cache (Redis / Memcached)

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": os.getenv("REDIS_URL"),
    }
}
```

## Celery (tarefas em background)

- Define `REDIS_URL` como broker.
- Exemplo de comando para worker:

```bash
celery -A myproject worker -l info
```

> Se precisares de workers separados, cria um serviço adicional no wolke com esse comando.

## Comandos de Deploy

```bash
git add .
git commit -m "Deploy Django"
git push origin main
```

## Troubleshooting

Consulta [troubleshooting.md](../troubleshooting.md) para erros comuns.

## Links Úteis

- Wolke: https://wolke.host/apps/create
- Django Docs: https://docs.djangoproject.com/
