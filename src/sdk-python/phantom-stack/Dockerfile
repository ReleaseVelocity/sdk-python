FROM python:3.12-slim

# hatch-vcs (setuptools_scm based) derives the version from git tags.
# This source tree has no .git directory, so provide a pretend version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is referenced by some dev tooling / pre-commit; portaudio dev headers are
# needed for the optional pyaudio (bidi) dependency to build if ever requested.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The wheel build target needs the package directory to exist for editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the source (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
