FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain a .git directory, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads the package layout).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[dev,all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Default to an interactive shell so developers can exec in and run commands.
CMD ["sleep", "infinity"]
