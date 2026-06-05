FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide a deterministic fallback version so the
# editable install succeeds.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System dependencies:
#  - portaudio19-dev: required to build pyaudio (bidi extra)
#  - git: used by some tooling / vcs-aware builds
#  - build-essential: native module compilation (pyaudio, etc.)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata generation).
COPY src ./src

# Install the project in editable/development mode with the full dependency
# set: all model-provider extras, bidi (audio/realtime), and dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
