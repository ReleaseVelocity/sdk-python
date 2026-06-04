FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# git is required because the project uses hatch-vcs (versioning from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# The package version is derived from git tags via hatch-vcs.
# Provide a static version so the build does not require a real git history/tag.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_NO_HOOKS=0

# Copy the rest of the source
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/dev mode with all optional features
# and dev/test dependencies.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0 && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
