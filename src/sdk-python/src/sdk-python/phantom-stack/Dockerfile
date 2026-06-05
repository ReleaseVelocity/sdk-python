FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree shipped
# here has no .git, so provide an explicit pretend version (honored by
# setuptools_scm, which hatch-vcs uses under the hood).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package layout so the editable install can resolve packages
# during dependency resolution. The full source is copied below.
COPY src ./src

# Install the project in editable/development mode with all runtime extras and
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
