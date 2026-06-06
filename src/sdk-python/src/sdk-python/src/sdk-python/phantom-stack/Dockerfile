FROM python:3.12-slim

# hatch-vcs (setuptools-scm) determines the version from git tags. There is no
# git history in this build context, so provide a pretend version to satisfy it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling. Most wheels are prebuilt; build-essential covers any
# source-only deps that need compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__version__)"]
