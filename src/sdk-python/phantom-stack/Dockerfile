FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree provided has no
# git history, so supply a fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build tooling occasionally needed for native wheels (e.g. some transitive deps).
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install has the package available.
COPY src ./src

# Install the project in editable/development mode with all optional feature
# groups (matches the hatch-test env: features = ["all"]) plus dev tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
