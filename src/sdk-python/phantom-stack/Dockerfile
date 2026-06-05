FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: required to build pyaudio (bidi extra)
# - git: hatch-vcs invokes git during the build backend
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for editable install of the package).
COPY . .

# Install the project in editable/development mode with ALL extras + dev tools.
# python>=3.12 enables the bidi extras (aws_sdk_bedrock_runtime, smithy-aws-core).
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
