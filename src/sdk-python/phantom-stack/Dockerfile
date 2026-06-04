FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo here, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy manifest first to leverage layer caching for the dependency install.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src/ ./src/

# Install the project in editable/development mode with all runtime extras and
# the dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
