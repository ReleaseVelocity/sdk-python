FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required because the project uses hatch-vcs (version derived from git tags)
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

# hatch-vcs derives the version from git tags. The source was delivered without
# a .git directory, so initialize a minimal repo with a tag to satisfy the build
# backend during editable install.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v0.0.0

# Install the project in editable/dev mode with ALL optional deps + dev/test tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
