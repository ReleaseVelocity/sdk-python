FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. There may be no git
# history/tags available in this build context, so provide a pretend version
# to both the setuptools-scm and hatch-vcs backends.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by the hatch-vcs build backend; keep build tooling minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / metadata generation).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test tooling so tests, linters and type checkers
# are all available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["bash"]
