FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build works without a populated .git directory.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio19-dev is needed for pyaudio (bidi extra); git for hatch-vcs tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with all + dev dependencies.
# A throwaway src tree lets the editable install resolve the package metadata
# before the real source is copied in.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install -e ".[all,dev]" \
    && rm -rf src

# Copy the full source.
COPY . .

CMD ["sleep", "infinity"]
