FROM python:3.11-slim

# hatch-vcs derives the package version from git tags. The build context does
# not include a .git directory, so provide a fallback version via the
# setuptools-scm pretend-version mechanism (honored by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest and package source first for better layer caching.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
