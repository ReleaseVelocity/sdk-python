FROM python:3.12-slim

# System deps:
# - git: hatch-vcs build backend and pre-commit may inspect git metadata
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra) if pulled in
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version (respected by hatch-vcs/setuptools-scm).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed by editable install / build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies (model providers, a2a, otel, docs) plus dev/test tooling.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
