FROM python:3.12-slim

# hatch-vcs determines version from git tags. The build context has no git
# history, so we provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is referenced by hatch-vcs / hatch tooling; keep build deps minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-fetch dependencies using the manifest (source not yet present, so this
# may be a partial cache layer; the editable install below completes it).
RUN pip install --upgrade pip

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with ALL extras and dev tools
# (pytest, mypy, ruff, moto, etc.). This mirrors the project's test environment
# which uses features = ["all"] plus the dev dependency group.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
