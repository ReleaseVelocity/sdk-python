FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git directory, provide a pretend version so the build/install succeeds.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# portaudio19-dev is required to build pyaudio (bidi extra). gcc for any
# native builds. git for hatch-vcs tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata step works
# before the full source copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev/test dependencies in editable mode.
RUN pip install --upgrade pip && \
    pip install -e ".[all,bidi-all,dev]"

# Copy the full project source.
COPY . .

CMD ["sleep", "infinity"]
