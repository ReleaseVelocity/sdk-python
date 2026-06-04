FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. Since the build
# context may not contain a .git directory, provide a fallback version so the
# editable install of strands-agents succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs version detection if .git is present
# - build tools + portaudio: needed for pyaudio (bidi extra) and any native builds
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libffi-dev \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy dependency manifest + sources needed to resolve
# the editable install first. hatchling needs the package source present.
COPY pyproject.toml README.md ./
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional features,
# bidi features, and dev/test tooling so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
