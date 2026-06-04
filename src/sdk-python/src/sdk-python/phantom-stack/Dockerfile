FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no .git,
# so provide a fallback version that hatchling/hatch-vcs will honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build tools occasionally needed for native wheels (e.g. pyaudio is excluded via bidi,
# but keep gcc available for any source builds). Kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed because the project installs editable and
# hatchling reads the package at build time).
COPY src/ ./src/

# Install the project in editable/development mode with all optional features
# plus dev/test tooling. Exclude the 'bidi' extras which require system audio
# libraries (pyaudio/portaudio) and are intentionally excluded from default tests.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
