
FROM python:3.10-slim as builder

WORKDIR /app


RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc python3-dev && \
    rm -rf /var/lib/apt/lists/*


ENV POETRY_VERSION=1.8.2
RUN pip install --user "poetry==$POETRY_VERSION"


COPY pyproject.toml poetry.lock manage.py ./
COPY demo demo/


ENV PATH="/root/.local/bin:${PATH}"
RUN poetry config virtualenvs.create false && \
    poetry install --no-interaction --no-ansi --only main


FROM python:3.10-slim

WORKDIR /app


COPY --from=builder /usr/local/lib/python3.10/site-packages /usr/local/lib/python3.10/site-packages
COPY --from=builder /app /app


ENV PYTHONPATH=/app
ENV DJANGO_SETTINGS_MODULE=demo.settings
ENV DJANGO_ALLOW_ASYNC_UNSAFE=true


ENV DATABASE_URL=sqlite:///db.sqlite3

EXPOSE 8000

CMD ["sh", "-c", "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"]