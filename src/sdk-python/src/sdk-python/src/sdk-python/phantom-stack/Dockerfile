FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a fallback version via setuptools_scm's pretend-version env var
# (hatch-vcs uses setuptools_scm under the hood).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
