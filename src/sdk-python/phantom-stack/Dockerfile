FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This repo has no .git, so we
# provide a pretend version to let the editable build succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dirs so the editable install has the package layout it
# expects, then copy the real sources.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional
# dependencies plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
