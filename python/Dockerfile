FROM python:3.10-alpine AS builder

WORKDIR /app

RUN apk add --no-cache --virtual .build-deps \
    gcc \
    musl-dev \
    postgresql-dev

RUN pip install --no-cache-dir poetry

COPY pyproject.toml poetry.lock ./

RUN poetry config virtualenvs.in-project true && \
    poetry install --only main --no-interaction --no-root && \
    find /app/.venv -type d -name '__pycache__' -exec rm -rf {} + && \
    find /app/.venv -type f -name '*.py[co]' -delete

FROM python:3.10-alpine

WORKDIR /app

RUN apk add --no-cache libpq

COPY --from=builder /app/.venv .venv
ENV PATH="/app/.venv/bin:$PATH"

COPY . .

RUN adduser -D django && \
    mkdir -p /app/static && \
    chown -R django:django /app

USER django

ENV PYTHONUNBUFFERED=1 \
    DJANGO_SETTINGS_MODULE=onxp.settings \
    STATIC_ROOT=/app/static

RUN python manage.py collectstatic --noinput

EXPOSE 8000
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--threads", "2", "onxp.wsgi:application"]