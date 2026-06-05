FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain a .git directory, provide a fallback version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps: git for hatch-vcs, build tools for any native wheels (pyaudio etc.)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install build backend so the editable install resolves quickly.
RUN pip install --no-cache-dir hatchling hatch-vcs

# Copy the full source.
COPY . /app

# Install the project in editable/development mode with all extras + dev tools.
# 'all' pulls every model-provider extra; 'dev' pulls pytest, ruff, mypy, moto, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
