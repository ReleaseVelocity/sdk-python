FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version (consumed by setuptools_scm / hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling. pyaudio (bidi extra) needs portaudio headers; build
# tools cover any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependency sets without the package itself to warm the cache.
# We install the project editable below once full source is present.
RUN pip install --upgrade pip

# Copy the full source tree.
COPY . /app

# Install the project in editable/development mode with ALL runtime extras,
# the bidi extras (Python 3.12), and the dev/test toolchain.
RUN pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
