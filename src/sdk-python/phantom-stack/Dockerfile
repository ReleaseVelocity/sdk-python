FROM python:3.12-slim

# Avoid interactive prompts and ensure consistent Python behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no git history, so provide an explicit pretend version to allow editable
# installs to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev

# Default AWS settings so boto3-backed tests that build clients before the moto
# fixtures activate do not fail with NoRegionError. These are dummy values used
# only for local/test execution and can be overridden at runtime.
ENV AWS_DEFAULT_REGION=us-west-2 \
    AWS_ACCESS_KEY_ID=test \
    AWS_SECRET_ACCESS_KEY=test

# git is needed by some tooling (pre-commit, hatch-vcs); build-essential is kept
# minimal and only added in case a dependency needs to compile.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        build-essential && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the development/test toolchain.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
