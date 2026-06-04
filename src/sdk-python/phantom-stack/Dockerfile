FROM python:3.12-slim

# hatch-vcs (via setuptools-scm) derives the version from git tags. The source
# tree has no git repository, so provide an explicit pretend version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio headers; git is useful for dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with all optional features,
# dev tooling, and bidi extras so every project command is available.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
