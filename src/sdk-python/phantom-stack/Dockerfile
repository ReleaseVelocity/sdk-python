FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs at build/runtime for version metadata
# - portaudio19-dev + gcc/build tools: needed if bidi (pyaudio) extras are installed
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm based) needs a version when there are no git tags.
# Provide a pretend version so the build does not fail in a non-git checkout.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves, then install deps.
# We install the full dependency set ([all]) plus dev/test tooling so a developer
# can run tests, linters, type checks, etc.
COPY src/ ./src/

RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["bash"]
