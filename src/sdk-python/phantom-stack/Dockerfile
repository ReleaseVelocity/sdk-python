FROM python:3.12-slim

# hatch-vcs (setuptools-scm) needs a version; no git tags available in this build context
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# build-essential + portaudio19-dev are needed to build pyaudio (bidi extra).
# git is useful for hatch-vcs and general dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install)
COPY src ./src

# Install the project in editable/development mode with ALL dependencies:
# runtime extras (all), bidi extras (bidi-all), and dev/test tooling (dev).
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
