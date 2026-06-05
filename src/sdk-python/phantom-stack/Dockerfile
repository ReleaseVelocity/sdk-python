FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no git history, so provide a pretend version (consumed by setuptools-scm which
# hatch-vcs builds on top of) to allow an editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package).
COPY src ./src

# Install the project in editable/development mode with the full feature set
# ("all") plus the dev/test tooling so tests, linters and type checks all work.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
