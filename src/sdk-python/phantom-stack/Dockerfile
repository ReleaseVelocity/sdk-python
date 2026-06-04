FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git repo in this
# build context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal system deps. portaudio is needed only if pyaudio (bidi extra) is
# installed; we keep it available so the optional bidi tooling can build.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all]" -e ".[dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
