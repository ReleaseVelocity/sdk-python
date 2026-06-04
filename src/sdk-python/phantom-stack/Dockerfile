FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to let the build/install succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest and source needed for an editable install first
# so dependency resolution can be cached across source-only changes.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with dev + all extras so
# tests, linters (ruff, mypy) and every optional integration are available.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
