FROM python:3.13-slim

# hatch-vcs derives the package version from git tags; provide a fallback so
# the build does not depend on git metadata being present.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / hatchling at build time; build tools may be
# required by some transitive deps (e.g. audio/native wheels fallbacks).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The package needs the source tree present to build in editable mode, but we
# install dependencies in a way that benefits from caching where possible.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional model
# providers ("all") and the dev/test toolchain ("dev"). This matches the
# project's hatch-test environment which uses features = ["all"].
RUN pip install -e ".[all,dev]"

# Default AWS region so boto3-based code/tests have a sane default.
ENV AWS_DEFAULT_REGION=us-west-2

CMD ["sleep", "infinity"]
