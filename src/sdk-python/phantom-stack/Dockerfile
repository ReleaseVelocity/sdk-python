FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# directory, so provide a pretend version to satisfy the build backend.
ARG PKG_VERSION=0.1.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# pyaudio (bidi extra) needs portaudio headers; build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install with hatchling).
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
