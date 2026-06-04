FROM python:3.12-slim

# System deps:
# - git: project uses hatch-vcs; harmless to have
# - build-essential: some deps (pyaudio etc. via bidi) may need compilation
# - portaudio19-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools_scm based) determines version from git tags.
# This repo has no .git directory, so provide a pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install build.
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
