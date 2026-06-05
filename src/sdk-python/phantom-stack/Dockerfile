FROM python:3.13-slim

# hatch-vcs derives the version from git. The source tree has no .git history,
# so initialize a throwaway repo with a tag to provide a deterministic version.
ARG PRETEND_VERSION=0.0.0
ENV PRETEND_VERSION=${PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Minimal system deps. git is useful for pre-commit / hatch-vcs in the running
# container; build-essential covers any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install of the package).
COPY src ./src

# Initialize a minimal git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/development mode with ALL optional
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN git init -q . \
    && git config user.email "build@local" \
    && git config user.name "build" \
    && git add -A \
    && git commit -qm "build" \
    && git tag "v${PRETEND_VERSION}" \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
