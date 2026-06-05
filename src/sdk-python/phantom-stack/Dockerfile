FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context here is not a
# git repository, so provide a pretend version to satisfy setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps: git (hatch-vcs), build tools for any native wheels, and
# portaudio for the optional pyaudio dependency used by the bidi extras.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching. The package version
# is dynamic (git tags) so we pin it via SETUPTOOLS_SCM_PRETEND_VERSION above.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable / dev mode).
COPY src ./src

# Install the project in editable/development mode with all runtime extras and
# the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
