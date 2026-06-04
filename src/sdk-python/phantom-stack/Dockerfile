FROM python:3.12-slim

# hatch-vcs (setuptools_scm based) derives the version from git tags.
# The source tree has no .git, so provide a pretend version to avoid build failures.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is occasionally needed by hatch-vcs / pre-commit tooling
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling)
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
