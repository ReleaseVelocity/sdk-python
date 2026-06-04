FROM python:3.12-slim

# System deps: git is required by hatch-vcs to determine the package version.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs reads version from git tags; with no real tags, pretend.
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the source package so editable install can resolve it.
COPY src/strands ./src/strands

# Initialize a throwaway git repo + tag so hatch-vcs can resolve a version
# even though HATCH_VCS_PRETEND_VERSION is also set as a safety net.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL extras + dev tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
