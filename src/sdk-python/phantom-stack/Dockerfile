FROM python:3.11-slim

# hatch-vcs determines the version from git tags. Allow override so the build
# does not depend on the project being a real git checkout.
# hatch-vcs is backed by setuptools-scm, which reads this env var to pretend a
# version when no git history/tags are available (the workspace is not a git repo).
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs at build time; build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-create source dir so editable install can resolve packages during dep resolution.
COPY src ./src

# hatch-vcs derives the version from git. The workspace is not a git checkout, so
# initialize a throwaway repo with a tag to give setuptools-scm a version to read.
RUN git init -q && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A && git commit -qm "build" && git tag v0.0.0

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
