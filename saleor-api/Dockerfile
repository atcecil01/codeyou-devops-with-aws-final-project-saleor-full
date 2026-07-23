FROM python:3.12

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        gettext \
        libffi8 \
        libgdk-pixbuf-2.0-0 \
        liblcms2-2 \
        libopenjp2-7 \
        libssl3 \
        libtiff6 \
        libwebp7 \
        shared-mime-info \
        xdg-utils \
        libpq5 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV POETRY_VERSION=1.8.4 \
    POETRY_VIRTUALENVS_CREATE=false \
    POETRY_NO_INTERACTION=1
RUN pip install --no-cache-dir "poetry==$POETRY_VERSION"

WORKDIR /app
COPY pyproject.toml poetry.lock ./
RUN poetry install --no-root \
    && rm -rf /root/.cache/pypoetry /root/.cache/pip
COPY . .

RUN groupadd -g 10001 saleor
RUN useradd -g saleor saleor 
RUN chown -R saleor:saleor /app

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

EXPOSE 8000

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]

