FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# checkout here, so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/import pyaudio (used by the optional bidi extra,
# but harmless to include for a complete dev environment).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable mode).
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
