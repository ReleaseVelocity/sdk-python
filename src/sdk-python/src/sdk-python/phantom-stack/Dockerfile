FROM python:3.13-slim

# Allow overriding the version used by hatch-vcs (project uses git tags for versioning)
ARG STRANDS_VERSION=0.1.0
ENV HATCH_BUILD_HOOKS_ENABLE=0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
# - git: hatch-vcs reads version from git tags
# - build-essential: in case any dependency needs to compile native extensions
RUN apt-get update && \
    apt-get install -y --no-install-recommends git build-essential && \
    rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs requires a git repo with a tag to compute the version.
# Initialize a lightweight repo and tag it so the editable install succeeds.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag "v${STRANDS_VERSION}"

# Install the project in editable/development mode with ALL extras and dev/test tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
