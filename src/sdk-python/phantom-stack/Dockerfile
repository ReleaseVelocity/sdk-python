FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. The source is
# shipped without a .git directory, so provide a pretend version to satisfy the
# build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs build backend invokes git
# - build-essential: some transitive deps may need compilation
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . .

# Install the project in editable/development mode with all optional features,
# bidi features, and dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all,bidi-all]"

CMD ["sleep", "infinity"]
