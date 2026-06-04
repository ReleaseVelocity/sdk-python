FROM python:3.12-slim

# Allow hatch-vcs/setuptools_scm to resolve a version without git tags
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. pyaudio (bidi extra) needs portaudio headers; build tools
# for any sdist-only packages.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for editable install
COPY src ./src

# Install the project in editable/development mode with ALL dependencies
# (all extras + dev/test tooling). hatch is included via dev extra.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["bash"]
