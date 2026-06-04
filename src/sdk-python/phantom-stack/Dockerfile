FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree is not a
# git repo, so we provide a pretend version to the setuptools-scm compatible
# backend used by hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by hatch-vcs at build time as a fallback; build-essential is
# kept minimal and only added if a wheel needs compiling.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for an editable install of the package).
COPY . /app

# Initialize a git repo with a tag as an extra safety net for hatch-vcs, then
# install the project in editable/development mode with ALL extras plus the dev
# and test tooling so a developer can run tests, linters, and type checks.
RUN git init -q && git add -A && git -c user.email=dev@local -c user.name=dev commit -qm init \
    && git tag v1.0.0 \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
