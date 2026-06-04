FROM python:3.12-slim

# Avoid interactive prompts and ensure stdout is unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (setuptools_scm) derives the version from git tags. There's no
# git history in the build context, so provide a pretend version instead.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps:
# - portaudio19-dev + gcc/build tools: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all extras +
# dev/test tooling. python 3.12 enables the bidi aws_sdk_bedrock_runtime extra.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all,bidi,bidi-gemini,bidi-openai]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
