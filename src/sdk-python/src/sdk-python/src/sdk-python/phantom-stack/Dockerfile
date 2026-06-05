FROM python:3.13-slim

# hatch-vcs derives the version from git tags; no .git is present in the build
# context, so we provide a pretend version (consumed by setuptools-scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev is needed by pyaudio (pulled in via the bidi extra); gcc/build
# tooling covers any source-only wheels. Installed only if needed by deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install.
COPY src/strands ./src/strands

# Install the project in editable/dev mode with all runtime extras plus the dev
# (test/lint) toolchain so a developer can run tests, mypy, ruff, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
