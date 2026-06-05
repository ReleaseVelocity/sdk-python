FROM python:3.12-slim

# Install git (needed by hatch-vcs to derive the package version from tags)
RUN apt-get update && \
    apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs reads the version from git. The source tree is not a git repo,
# so we initialize one with a tag to satisfy the dynamic version.
COPY . /app

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL extras + dev/test tools
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
