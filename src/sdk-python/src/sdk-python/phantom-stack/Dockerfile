FROM python:3.12-slim

# hatch-vcs (via setuptools-scm) derives version from git tags; no git history
# is shipped here, so provide a pretend version for the build.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# System deps: build-essential for any native wheels, portaudio for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source (needed for editable install of strands package)
COPY src ./src

# Install in editable/dev mode with all extras + dev tooling.
# bidi-all pulls aws_sdk_bedrock_runtime / smithy which require py>=3.12 (satisfied).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev,bidi]"

# Copy the rest of the project (tests, configs)
COPY . .

CMD ["sleep", "infinity"]
