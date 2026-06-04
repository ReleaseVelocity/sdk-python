FROM python:3.12-slim

# hatch-vcs derives version from git tags; provide a fallback so the
# build does not require git history/tags to be present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependency closure (editable install needs sources, but we
# warm the layer cache with the manifest available).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY docs ./docs

# Install the project in editable/development mode with all runtime
# extras plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
