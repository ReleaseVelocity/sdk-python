FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history in this
# build context, so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs version source; build tools occasionally needed by
# transitive deps. Keep it minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install since hatchling reads it).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests, linters, type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__name__, 'ready')"]
