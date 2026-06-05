FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. Since the build
# context has no .git history, provide a pretend version so the editable install
# can compute the package version without failing.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Native deps: pyaudio (bidi extra) needs portaudio + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libffi-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/dev mode with all runtime + dev + bidi deps.
# Editable install requires the package source; copy the rest of the project.
COPY . .

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi]"

CMD ["sleep", "infinity"]
