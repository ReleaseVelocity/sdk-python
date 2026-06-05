FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / metadata).
COPY src/ ./src/

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters, and type checkers are available.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
