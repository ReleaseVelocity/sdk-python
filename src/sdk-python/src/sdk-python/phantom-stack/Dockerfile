FROM python:3.12-slim

# Tell hatch-vcs the version since no real git tags are present in the build context.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs (version source = "vcs").
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for editable install / package discovery).
COPY . .

# Initialize a minimal git repo with a tag so hatch-vcs can resolve a version
# even if the build context lacks git history, then install in editable/dev mode
# with all optional deps and dev/test tooling.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "build" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
