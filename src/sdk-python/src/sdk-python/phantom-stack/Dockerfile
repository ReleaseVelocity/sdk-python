FROM python:3.12-slim

# Avoid interactive prompts and ensure clean python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# git is needed because the project derives its version from git tags (hatch-vcs)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs git metadata to compute a version. The build context does not
# include .git, so provide a fallback version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with ALL extras and dev tools
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
