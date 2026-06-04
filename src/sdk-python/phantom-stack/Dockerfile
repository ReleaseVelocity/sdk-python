FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide a pretend version (consumed by setuptools_scm / hatch-vcs).
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
