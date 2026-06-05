FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required for hatch-vcs to determine the package version
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so initialize a throwaway repo with a tag so the build can resolve
# a version. The pretend-version env var is the fallback used at install time.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0

# Copy the rest of the source
COPY . .

# Re-commit full source so hatch-vcs sees a clean tree at the tagged version
RUN git add -A && git commit -qm "src" --allow-empty && git tag -f v0.0.0

# Install the project in editable/dev mode with all optional deps + dev tools
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
