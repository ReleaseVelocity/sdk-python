FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the project uses hatch-vcs (version from git tags).
# We provide a fallback version below so a git history is not strictly required.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. Since the source tree may not
# include a git history, provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
