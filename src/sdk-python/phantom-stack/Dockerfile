FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is copied
# without git history, provide a pretend version so the build backend works.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most deps are pure-python wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy the project (source, tests, configs, docs). hatch-vcs needs the full
# tree, so we copy everything up front.
COPY . .

# hatch-vcs (setuptools-scm) requires a git repo with a tag to derive the
# version. The source is copied without git history, so initialize a
# throwaway repo with a tag matching the configured format (v$version).
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "dev container snapshot" \
    && git tag v0.0.0

# Install the project in editable/development mode with all runtime
# extras plus dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
