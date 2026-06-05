FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git
# directory, so we provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs/setuptools-scm; build tools occasionally needed
# for native wheels (e.g. pyaudio in bidi extra is excluded so kept minimal).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install resolves before full source copy.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# and the dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
