FROM dhi.io/python:3.14-dev@sha256:d596be714808bdbaff280777fb66bd0d018c07a60b2f04558960900690ddb438 AS build

ENV PYTHONDONTWRITEBYTECODE=1 \
    PATH="/app/venv/bin:$PATH"

WORKDIR /app
RUN python -m venv /app/venv
COPY requirements.txt .
RUN pip install --no-cache-dir --no-compile --require-hashes --no-deps -r requirements.txt \
    && python -m pip uninstall --yes pip

FROM dhi.io/python:3.14@sha256:3b96bee5f18a1978b7093b46f9320b9f55e266339ac75d6fca5ebe0328c553c6

ENV OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PATH="/app/venv/bin:$PATH"

WORKDIR /app
COPY --from=build --chown=65532:65532 /app/venv /app/venv
COPY --chown=65532:65532 app.py ./
COPY --chown=65532:65532 templates ./templates

USER 65532:65532
EXPOSE 8080
CMD ["opentelemetry-instrument", "gunicorn", "--bind=0.0.0.0:8080", "app:app"]
