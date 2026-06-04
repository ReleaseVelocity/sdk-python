FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. There is no
# .git in this build context, so provide a pretend version to satisfy the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# Minimal build tooling. Most wheels are prebuilt; build-essential covers any
# source builds (e.g. some opentelemetry / pydantic-core fallbacks).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source so the editable install can resolve the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install in editable/development mode with all model providers and the full
# dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
