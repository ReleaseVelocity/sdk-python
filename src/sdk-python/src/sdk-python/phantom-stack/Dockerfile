FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The source tree
# may not be a git repo, so provide a pretend version so the editable install
# does not fail. Can be overridden at build time.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${SETUPTOOLS_SCM_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# System deps: git (for hatch-vcs fallback), and portaudio for the optional
# pyaudio (bidi) dependency in case it gets pulled in. build-essential covers
# any native builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/dev mode with all extras + dev tooling.
# "all" provides the model-provider extras the test-suite imports; "dev"
# provides pytest, moto, ruff, mypy, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
