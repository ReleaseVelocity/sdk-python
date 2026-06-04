FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide a pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by some dev tooling (pre-commit, hatch-vcs); build tools kept
# minimal and only added if native builds require them.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching. README is referenced by
# pyproject (readme = "README.md") so it must be present for the build.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src/ ./src/

# Install the project in editable/development mode with ALL extras plus the
# dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
