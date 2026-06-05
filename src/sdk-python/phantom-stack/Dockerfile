FROM python:3.13-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The build context
# has no .git directory, so provide a pretend version for the package build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the project (source, tests, configs).
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
