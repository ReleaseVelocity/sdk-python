FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git repo in the
# build context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Build/runtime system deps. git is useful for dev workflows; build-essential
# in case any transitive dependency needs to compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install the full dependency set. We install the project itself afterwards
# in editable mode once the full source is present.
RUN pip install --no-cache-dir hatchling hatch-vcs hatch

# Copy the rest of the source.
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
