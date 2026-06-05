FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. The source tree
# is copied without git history, so provide a fallback version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Install minimal system deps. portaudio is required to build pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the full source.
COPY . .

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
