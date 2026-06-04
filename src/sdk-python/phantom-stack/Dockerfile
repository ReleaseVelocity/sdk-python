FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a pretend version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs / general dev usage
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: required by pyaudio (bidi optional dependency)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# dev tooling, and the bidi extras so every project command is available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

# Copy the remainder of the project (tests, configs, docs, etc.).
COPY . .

CMD ["sleep", "infinity"]
