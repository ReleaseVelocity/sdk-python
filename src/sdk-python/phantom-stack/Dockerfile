FROM python:3.13-slim

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs to derive the package version; build-essential
# covers any source-built dependency wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project (src layout, tests, configs).
COPY . .

# hatch-vcs derives the version from git. The build context has no .git, so
# initialize a throwaway repo with a tag to satisfy setuptools-scm/hatch-vcs.
RUN git init -q && git config user.email dev@local && git config user.name dev \
    && git add -A && git commit -qm "dev build" && git tag v0.0.0

# Install the project in editable/development mode with all runtime extras and
# the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
