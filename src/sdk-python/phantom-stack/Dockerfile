FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git repository, provide an explicit version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable (dev-mode) install.
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
