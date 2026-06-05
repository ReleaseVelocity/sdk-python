FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# metadata, so provide a pretend version to let the build backend succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# git is required by hatch-vcs build backend; build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the rest of the project (tests, configs, docs).
COPY . .

# hatch-vcs (setuptools-scm) needs git metadata to derive the version. The
# source tree ships without .git, so initialise a throwaway repo with a tag.
RUN git init -q && git config user.email dev@example.com \
    && git config user.name dev && git add -A \
    && git commit -qm "containerize" && git tag v0.0.0

# Install the project in editable mode with all optional features plus the
# dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
