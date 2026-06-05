FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context may not
# include git history, so provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by the hatch-vcs build backend; build-essential covers any
# transitive native dependency that needs compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source and tests (editable install reads the layout at
# install time, and tests are needed for the dev workflow).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional features
# plus the full dev toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
