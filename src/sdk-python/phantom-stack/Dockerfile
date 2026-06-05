FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to allow the editable install to work.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# git is needed so hatch-vcs can operate cleanly; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Initialize a minimal git repo so hatch-vcs has a tag to resolve a version from.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && git commit -q -m "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL extras plus dev tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
