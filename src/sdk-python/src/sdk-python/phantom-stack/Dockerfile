FROM python:3.12-slim

# All project dependencies (including the [all] and [dev] extras) ship as
# prebuilt manylinux wheels, so no system build toolchain is required.

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# hatch-vcs derives the version from git tags. There is no .git in this
# workspace, so provide an explicit pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
# Source tree is required because we install in editable mode.
COPY src ./src

# Install the project in editable/development mode with ALL extras plus the
# dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
