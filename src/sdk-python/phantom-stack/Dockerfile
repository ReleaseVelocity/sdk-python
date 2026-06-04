FROM python:3.12-slim

# hatch-vcs/setuptools-scm needs a version; source is not a git repo here.
ARG PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Build tooling for any native deps in the [all] extras
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution)
COPY src ./src

# Install the project in editable/development mode with all runtime
# extras plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
