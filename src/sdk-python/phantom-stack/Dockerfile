FROM python:3.13-slim

# Build dependencies for native extensions (e.g. pyaudio used by bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch uses git tags (hatch-vcs) for the dynamic version. The source tree may
# not include git metadata, so set a fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata can resolve
COPY src ./src

# Install the project in editable/development mode with all runtime extras + dev tools
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
