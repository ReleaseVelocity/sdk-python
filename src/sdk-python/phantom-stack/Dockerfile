FROM python:3.12-slim

# Version is normally derived from git tags via hatch-vcs. Since the build
# context has no git history, provide a pretend version so the editable
# install can compute a package version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
#  - git: hatch-vcs may shell out to git during build/runtime
#  - build-essential: some optional deps (e.g. audio/native wheels) may need it
#  - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install resolution.
COPY src ./src

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling. The "all" extra pulls every model provider.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
