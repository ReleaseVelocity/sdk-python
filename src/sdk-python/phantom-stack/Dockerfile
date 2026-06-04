FROM python:3.12-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. There is no git history in this
# build context, so provide a pretend version (consumed by setuptools-scm,
# which hatch-vcs builds upon).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

# System deps:
#  - git: required by hatch-vcs build backend
#  - portaudio19-dev + gcc/build tools: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable
# mode and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# and the dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
