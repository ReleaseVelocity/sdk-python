FROM python:3.13-slim

# System deps:
# - git: required by hatch-vcs to determine version from git tags
# - build-essential: for any native wheels that need compiling
# - portaudio19-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project (source, tests, .git for version detection)
COPY . .

# hatch-vcs needs git metadata. If .git is absent, provide a fallback version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_BUILD_HOOKS_ENABLE=true

# Install the project in editable/dev mode with ALL extras + dev tooling.
# bidi extras require python>=3.12 (satisfied here) but pyaudio needs portaudio (installed above).
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
