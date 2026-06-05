FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require a populated git history.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. Most wheels are prebuilt; git is kept available for
# hatch-vcs and general dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create source package directory so editable install can resolve it.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
