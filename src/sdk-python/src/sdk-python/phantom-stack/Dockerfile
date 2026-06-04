FROM python:3.12-slim

# System deps:
# - git: required because the project uses hatch-vcs to derive its version from
#   git tags. Without a git repo/tag the editable install fails.
# - build-essential: available in case any dependency needs to compile native code.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs determines the version from git tags. The source tree has no git
# metadata, so create a throwaway repo with a tag purely so the build backend
# can resolve a version during the editable install. This does not modify the
# original project sources outside the container.
RUN git init -q \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "container build" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
