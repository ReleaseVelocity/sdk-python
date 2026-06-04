FROM python:3.12-slim

# Git is required: the project uses hatch-vcs to derive its version from git tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Allow pip to install into the system environment
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy full source
COPY . /app

# hatch-vcs needs a git repo with a tag to compute the version.
# If the source isn't already a git repo, initialize one with a baseline tag.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "snapshot" && \
        git tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with all optional and dev deps.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
