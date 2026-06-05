FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Some optional deps (e.g. pyaudio for bidi) need build tooling/headers.
# Install only what the full dependency set needs to compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip tooling
RUN pip install --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for an editable install (package + version metadata)
COPY src ./src

# Install the project in editable/development mode with dev + all optional deps
RUN pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
