FROM python:3.10-slim

# System Package
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    libopenblas-dev \
    && rm -rf /var/lib/apt/lists/*

# Working Directory
WORKDIR /app

# Copy Project Source
COPY . /app

# Python virtual env of uv
RUN pip install --no-cache-dir uv \
    && uv venv --python 3.10 \
    && . .venv/bin/activate \
    && uv sync --all-packages

ENV PATH="/app/.venv/bin:$PATH"

ENTRYPOINT ["uv", "run", "cluefin-cli", "inquiry"]
