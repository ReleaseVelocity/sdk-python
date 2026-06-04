FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio, but bidi is excluded from the default
# test config and the [all] extra. We keep the image lean and only add git,
# which hatch-vcs expects for version resolution.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no git
# metadata, so provide an explicit version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src ./src

# Install the project in editable/dev mode with all runtime extras plus the
# dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
