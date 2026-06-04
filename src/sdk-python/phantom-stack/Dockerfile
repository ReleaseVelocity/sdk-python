FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source is provided without a
# git checkout, so we supply a pretend version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional providers
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
