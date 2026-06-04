FROM python:3.12-slim

# hatch-vcs (setuptools-scm) needs a version when there is no git metadata.
# Provide a pretend version so the editable install can build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. git is useful for dev workflows / pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the package).
COPY src ./src

# Install the project in editable/development mode with all optional features,
# dev tooling and test dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
