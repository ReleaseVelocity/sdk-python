FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version for the setuptools_scm backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build/runtime deps. portaudio + gcc are only needed if the optional "bidi"
# extra (pyaudio) is installed; kept minimal otherwise. git allows hatch-vcs to
# work if a repo is later mounted.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching. The hatchling build
# backend needs the package source to exist for an editable install, so we copy
# the manifest plus a minimal tree, then the full source.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL feature extras and
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Now copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
