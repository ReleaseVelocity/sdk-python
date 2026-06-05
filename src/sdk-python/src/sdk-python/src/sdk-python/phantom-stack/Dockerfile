FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no .git directory, so provide a fallback version for the build backend.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Bring in tests and remaining project files.
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__name__, 'ready')"]
