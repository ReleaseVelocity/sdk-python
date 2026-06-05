FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version requirement.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build dependencies for any native wheels (pyaudio needs portaudio for bidi,
# build-essential for source builds). Keep minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install resolution.
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus dev/test tooling so a developer can run tests, linters, type checks.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
