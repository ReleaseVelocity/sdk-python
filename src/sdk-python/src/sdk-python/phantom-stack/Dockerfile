FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs (setuptools_scm based) needs a version; there is no git repo
    # inside the image so provide a pretend version for the editable install.
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Minimal system deps. portaudio is required by pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install resolution.
COPY src ./src

# Install the project in editable/development mode with the full dependency
# set: runtime "all" providers + "dev" tooling (pytest, ruff, mypy, moto, etc.)
# plus "a2a" (server extras) so the complete unit test suite can run.
RUN pip install -e ".[all,dev,a2a]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
