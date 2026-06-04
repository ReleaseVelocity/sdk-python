FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the source tree has no
# git metadata, provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest and project metadata first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install build backend resolution).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
