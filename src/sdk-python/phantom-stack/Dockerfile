FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines the version from git tags. The source
# tree provided here is not a git checkout, so we provide a pretend version to
# allow the editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve packages).
COPY src/ ./src/

# Install the project in editable/development mode with all model-provider
# extras plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
