FROM python:3.12-slim

# Git is required because the project uses hatch-vcs to derive its version
# from git tags at build/install time.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git repo with a tag to determine the version.
# Initialize a repo and tag it so the editable install succeeds.
RUN git init -q \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "containerized build" \
    && git tag v1.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
