# Strands Agents Python SDK - development image
# Project requires Python >=3.10; use slim variant.
FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a fallback version for the hatchling/hatch-vcs build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The wheel build target points at src/strands, which must exist for the
# editable install to succeed. Copy the full source tree.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
