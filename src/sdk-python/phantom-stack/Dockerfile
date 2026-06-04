FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the source is provided
# without a .git directory, supply a pretend version so the editable install
# succeeds. Override via build arg if needed.
ARG STRANDS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${STRANDS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is useful for dev workflows and pre-commit hooks.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/dev mode with ALL optional deps plus the
# dev test/lint toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
