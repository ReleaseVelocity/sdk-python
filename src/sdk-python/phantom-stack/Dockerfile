FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree has no
# .git history, provide a fallback version so the editable install succeeds.
ARG PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PRETEND_VERSION}
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System dependencies:
#  - build-essential / python3-dev: compile native wheels (e.g. pyaudio)
#  - portaudio19-dev: required headers for the optional `bidi` (pyaudio) extra
#  - git: used by some tooling / hatch
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        python3-dev \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source so the editable install can resolve the package.
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
