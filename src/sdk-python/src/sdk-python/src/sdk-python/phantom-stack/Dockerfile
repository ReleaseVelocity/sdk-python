FROM python:3.12-slim

# hatch-vcs reads the version from git tags. Since the build context may not be
# a git repo, provide a fallback version so hatchling can build the package.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; build tools occasionally needed for native wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install metadata step can resolve.
# Then copy the full source.
COPY . /app

# Initialize a git repo so hatch-vcs has something to read, and install the
# project in editable/development mode with ALL dependencies (runtime + dev +
# optional integrations) so tests and dev tools are available.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm "init" && \
    git tag v0.0.0 && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
