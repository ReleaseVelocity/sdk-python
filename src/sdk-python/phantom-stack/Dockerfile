FROM python:3.11-slim

# hatch-vcs derives the package version from git tags. There is no .git in the
# build context, so provide a pretend version (respected by hatch-vcs /
# setuptools_scm) to allow the editable install to succeed.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${SETUPTOOLS_SCM_PRETEND_VERSION} \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Note: the [all,dev] extras install entirely from prebuilt wheels, so no
# native compilation (build-essential) is required. The optional `bidi` extra
# (pyaudio/portaudio) is intentionally not installed.

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install to resolve.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
