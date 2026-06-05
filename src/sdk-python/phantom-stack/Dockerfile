FROM python:3.12-slim

# hatch-vcs reads the version from git tags; provide a fallback so the build
# does not depend on the project being a git checkout.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# git is needed by hatch-vcs to resolve the package version.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Initialize a throwaway git repo with a tag so hatch-vcs can determine a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v0.0.0

# Install the project in editable/development mode with all optional and dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
