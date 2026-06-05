FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no .git directory, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/import pyaudio (pulled in by the bidi extra).
# git is handy for dev workflows. Build deps cover any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with every optional and dev
# dependency group so a developer can run tests, linters and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
