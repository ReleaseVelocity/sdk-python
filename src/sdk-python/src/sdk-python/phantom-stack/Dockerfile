FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide a pretend version to allow the build/editable install
# to succeed.
ARG STRANDS_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

# Minimal system deps. git is useful for dev workflows and pre-commit.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Leverage layer caching: copy dependency manifest + readme (referenced by
# pyproject) before the full source tree.
COPY pyproject.toml README.md ./

# Pre-install dependency closure so the editable install at the end is fast and
# cached. We install the project itself with all + dev extras.
COPY src ./src
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

# Re-run editable install in case copying full source changed package metadata.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
