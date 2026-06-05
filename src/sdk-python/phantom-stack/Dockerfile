FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a git repository, provide a fallback version via setuptools_scm's
# pretend-version mechanism (used internally by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System packages: git (so hatch-vcs/setuptools_scm can operate if a repo is
# present) and build tooling for any native deps (e.g. pyaudio is excluded by
# default but keep gcc available for safety on optional builds).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves, then install
# dependencies. We do a non-editable resolve of deps via the manifest copy by
# creating a minimal package tree before the full source copy.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
