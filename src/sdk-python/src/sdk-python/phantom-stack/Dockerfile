FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# editable install does not fail in the absence of git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install minimal build/runtime tooling. git is referenced by hatch-vcs tooling
# and is generally useful for a dev container.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can build the wheel metadata.
COPY src ./src

# Install the project in editable/development mode with ALL extras plus the
# dev and test toolchains (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
