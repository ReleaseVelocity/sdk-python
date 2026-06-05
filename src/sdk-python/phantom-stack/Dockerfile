FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git metadata, provide a pretend version (hatch-vcs honors this env var).
ARG STRANDS_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# src is required for the editable install of the package.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
# "all" pulls in every model-provider extra; "a2a" adds the server stack; "dev"
# adds pytest, ruff, mypy, moto, etc.
RUN pip install --no-cache-dir -e ".[all,a2a,dev,otel]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
