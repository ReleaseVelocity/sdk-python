# Strands Agents Python SDK - development image
# requires-python >=3.10; use slim 3.12 (matches CI matrix)
FROM public.ecr.aws/docker/library/python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required by hatch-vcs (version derived from git tags).
# portaudio/build tools needed for optional bidi (pyaudio) deps if pulled in.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with a tag to compute the package version.
# The source is not a git repo, so create a minimal one (no source changes).
COPY src ./src
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v1.0.0

# Install the SDK in editable/dev mode with all optional + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[dev,all]" hatch

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Restore the git tag state after copying everything (.git from COPY . . may overwrite).
RUN rm -rf .git && \
    git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v1.0.0

CMD ["sleep", "infinity"]
