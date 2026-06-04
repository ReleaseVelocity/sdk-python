FROM python:3.12-slim

# Avoid interactive prompts and ensure stdout is unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required because the project derives its version from git tags
# (hatch-vcs). build-essential is included for any optional native builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the dependency/build manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs git metadata to compute a version. Since the build context
# does not include a .git directory reliably, tell hatch-vcs to use a fallback
# version via env var so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the full source tree.
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version even if
# .git was not part of the build context.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
