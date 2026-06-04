FROM python:3.12-slim

# git provides build tooling; hatch-vcs normally derives version from git tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm) version fallback (no .git present in build context).
# The env var must be normalized as SETUPTOOLS_SCM_PRETEND_VERSION_FOR_<DIST_NAME>.
ARG PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}

# Copy dependency manifests first for better layer caching
COPY pyproject.toml README.md ./

# Copy full source
COPY . .

# Install the project in editable/development mode with ALL extras + dev/test deps
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
