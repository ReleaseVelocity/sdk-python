FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context
# may not contain git metadata, provide a pretend version so the build
# backend can resolve a version without requiring git.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: pyaudio (bidi) needs portaudio; git for tooling; build tools
# for any packages lacking wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    gcc \
    g++ \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed for editable install to resolve the package).
COPY src ./src

# Install the project in editable/dev mode with all runtime + dev/test deps.
# Avoid the optional `bidi` extra (requires Python>=3.12 specific wheels and
# native audio); `all` covers every model provider plus docs and otel.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
