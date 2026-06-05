FROM python:3.12-slim

# hatch-vcs determines version from git tags. No git repo is present in the
# build context, so provide a pretend version for setuptools-scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# pyaudio (bidi extra) needs portaudio headers + a compiler for the C extension.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves.
COPY src ./src

# Install the project in editable/development mode with ALL dependencies:
#   - all       : every model-provider / optional feature extra
#   - dev        : test + lint + tooling deps (pytest, ruff, mypy, moto, ...)
#   - bidi-all   : bidirectional streaming extras (pyaudio, websockets, ...)
RUN pip install -e ".[all,dev,bidi-all]"

# Copy the rest of the source (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
