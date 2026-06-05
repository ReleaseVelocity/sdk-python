FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree may not include
# git history/tags, so provide a pretend version that setuptools_scm/hatch-vcs honors.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# System deps: portaudio for pyaudio (bidi extra), build tooling for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy only the build manifest first.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve before full source copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in development mode with all runtime + dev/test extras.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full source tree.
COPY . .

# Re-run install to ensure entry points/metadata reflect the full tree.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
