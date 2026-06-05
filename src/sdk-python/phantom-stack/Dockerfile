FROM python:3.12-slim

# Allow overriding the version that hatch-vcs would normally derive from git tags.
# The source tree is not a git checkout in this environment, so we provide a
# pretend version and also initialize a throwaway git repo with a tag as a fallback.
ARG PKG_VERSION=0.1.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs to resolve the package version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Initialize a git repo + tag so hatch-vcs can derive a version (belt & suspenders
# alongside SETUPTOOLS_SCM_PRETEND_VERSION).
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag "v${PKG_VERSION}"

# Install the project in editable/development mode with all optional + dev deps.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["python", "-c", "import strands; print('strands import OK')"]
