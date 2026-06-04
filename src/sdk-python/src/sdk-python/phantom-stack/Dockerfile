FROM python:3.13-slim

# hatch-vcs (setuptools_scm) determines version from git tags. There is no git
# repo in this context, so provide a pretend version for the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# All dependencies install from pre-built wheels; no system build tools needed.

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
COPY src/ ./src/

# Install the project in editable/dev mode with all optional features + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
