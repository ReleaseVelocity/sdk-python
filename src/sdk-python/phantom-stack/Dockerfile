FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a fallback version to keep the build deterministic.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / pre-commit; build tools are kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve metadata before
# the full source is copied.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional features,
# dev tooling, and test dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
