FROM python:3.11-slim

# hatch-vcs derives the package version from git tags. The build context has no
# git history, so we provide a fallback version via this env var.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV HATCH_VCS_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed because the build backend (hatch-vcs) inspects VCS metadata.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so an editable install has something to link against.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Initialize a throwaway git repo with a tag so hatch-vcs can resolve a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "containerize" \
    && git tag v1.0.0

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
