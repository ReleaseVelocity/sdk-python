FROM python:3.12-slim

# System deps: portaudio for pyaudio (bidi extra), build tools for any native wheels, git for tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source has no git history,
# so provide a pretend version to allow an editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install build)
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test tooling.
# 'all' pulls every model-provider extra; 'dev' adds pytest, ruff, mypy, moto, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
