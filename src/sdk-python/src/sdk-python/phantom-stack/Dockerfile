FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, tell setuptools_scm (used by hatch-vcs) to use a
# fixed pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
# - git: setuptools_scm/hatch-vcs probing fallback
# - portaudio19-dev: required to build pyaudio (bidi extra)
# - build-essential: native builds (pyaudio, etc.)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest and project metadata first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
