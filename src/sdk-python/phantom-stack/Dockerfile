FROM python:3.11-slim

# hatch-vcs/setuptools_scm needs a version since the .git dir is not present.
# This env var lets the build proceed without git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the source so the editable install can build the package.
COPY src ./src

# Install the project in editable/development mode with ALL optional deps
# (model providers, a2a, otel, docs) plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
