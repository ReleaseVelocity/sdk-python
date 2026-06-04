FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repo, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Minimal system deps. git is included so hatch-vcs / pre-commit behave even if
# a repo is present; build tools are kept out unless native builds fail.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip

# Copy dependency manifest and metadata first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in source needed for an editable (dev-mode) install.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands import OK')"]
