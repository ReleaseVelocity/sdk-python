FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version to satisfy the build backend.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves.
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
# `all` covers the optional model providers exercised by the unit tests;
# `dev` provides pytest, moto, ruff, mypy and other tooling.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
