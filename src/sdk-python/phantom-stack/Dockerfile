FROM python:3.12-slim

# hatch-vcs (setuptools-scm under the hood) derives the version from git tags.
# Since the build context has no git history, provide a pretend version so the
# editable install works. Set both the generic and dist-specific variables.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source (package code needed for editable install / version discovery).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
