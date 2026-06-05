FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# metadata, so provide a pretend version that hatch-vcs/setuptools-scm honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is occasionally consulted by the vcs version plugin; keep image lean.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve the build.
COPY src/ ./src/

# Install the project in editable/development mode with all feature extras
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
