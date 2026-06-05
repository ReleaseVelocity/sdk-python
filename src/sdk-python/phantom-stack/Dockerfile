FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no git
# repository, so provide a fallback version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling; most wheels are prebuilt but git is useful for tooling
# and some sdists may need a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching. The wheel build needs the
# README (referenced by readme = "README.md") and the package source.
COPY pyproject.toml README.md ./
COPY src ./src

# hatch-vcs (setuptools-scm based) needs a git repo with a tag to resolve the
# version. The build context has no .git, so create a minimal tagged repo.
RUN git init -q && \
    git config user.email "build@local" && \
    git config user.name "build" && \
    git add -A && \
    git commit -qm "build" && \
    git tag v0.0.0

# Install the project in editable mode with all optional features plus the dev
# toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
