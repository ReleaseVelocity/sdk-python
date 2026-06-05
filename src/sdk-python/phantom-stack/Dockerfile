FROM python:3.13-slim

# Version arg consumed by hatch-vcs (setuptools-scm based) so the build does
# not require git tags. Can be overridden at build time.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs metadata hooks; build tools for any native deps
RUN apt-get update \
    && apt-get install -y --no-install-recommends git build-essential portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies using the manifest. The package itself is installed
# editable later once the full source tree is present.
RUN pip install --no-cache-dir hatchling hatch-vcs

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional features
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
