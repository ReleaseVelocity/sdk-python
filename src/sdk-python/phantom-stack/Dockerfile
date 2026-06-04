FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the build context may not be a
# git repo, so provide a pretend version (consumed by setuptools_scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio headers; git is useful for tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL dependencies
# (all model providers + bidi extras + dev/test tooling).
COPY src ./src
RUN pip install -e ".[all,bidi-all,dev]"

# Now copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
