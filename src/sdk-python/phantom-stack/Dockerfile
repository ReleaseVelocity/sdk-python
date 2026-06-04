FROM python:3.12-slim

# Version for hatch-vcs (no .git present at build time); allow override
ARG PKG_VERSION=0.0.0+docker
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# System deps: git is required so hatch-vcs has a repo to read; build tools
# only if a native wheel needs compiling (kept minimal).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata) and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Initialize a git repo with a tag so hatch-vcs can derive a version,
# and install the project in editable/development mode with all + dev extras.
RUN git init -q && git add -A \
    && git -c user.email=dev@local -c user.name=dev commit -qm "docker build" \
    && git tag "v${PKG_VERSION%%+*}" \
    && pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[dev,all]"

CMD ["sleep", "infinity"]
