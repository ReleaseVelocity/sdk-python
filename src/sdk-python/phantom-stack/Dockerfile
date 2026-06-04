FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because hatch-vcs derives the version from git tags
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# hatch-vcs requires a git repo with a tag to determine the version.
# Initialize a minimal repo and tag so editable install can resolve version.
COPY . /app

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "container build" && \
    git tag v1.0.0

# Install the project in editable/development mode with all extras + dev tools
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
