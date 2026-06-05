FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, provide a pretend version so the editable install works.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    LOG_LEVEL=DEBUG

WORKDIR /app

# System deps:
# - build-essential: occasionally needed by transitive native builds
# - portaudio19-dev / libasound2-dev: required by pyaudio (bidi extra), mirrors CI
# - git: hatch-vcs / general dev convenience
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip tooling
RUN pip install --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves during dep layer.
# (Full source is copied below; this just primes the install.)
RUN mkdir -p src/strands

# Copy the full source tree
COPY . .

# Install the project in editable/development mode with all runtime extras,
# all dev/test tooling, and the bidi audio stack. This mirrors `hatch`'s
# default + test environments which use features = ["all"] / ["dev"].
RUN pip install -e ".[dev,all,bidi,bidi-gemini,bidi-openai]"

CMD ["sleep", "infinity"]
