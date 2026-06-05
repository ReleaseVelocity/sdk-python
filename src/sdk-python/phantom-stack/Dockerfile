FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no git
# metadata, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (required so the editable install can resolve the package).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional features
# and dev/test tooling so a developer can run tests, linters, type checks, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
