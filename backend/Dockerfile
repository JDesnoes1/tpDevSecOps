FROM python:3.12-slim AS quality
WORKDIR /src

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y --no-install-recommends git \
  && rm -rf /var/lib/apt/lists/*

COPY . /src

RUN pip install --no-cache-dir -r backend/requirements.txt \
  && pip install --no-cache-dir pre-commit \
  && pre-commit run --all-files --show-diff-on-failure

FROM python:3.12-slim AS runtime
WORKDIR /app/backend

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

RUN useradd -m appuser
USER appuser

COPY --chown=appuser:appuser backend/requirements.txt /app/backend/requirements.txt
RUN pip install --no-cache-dir -r /app/backend/requirements.txt

COPY --chown=appuser:appuser backend /app/backend

EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
