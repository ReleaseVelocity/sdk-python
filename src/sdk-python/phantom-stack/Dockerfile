# Strands Agents Python SDK - development container
# Project requires Python >=3.10; use slim image.
FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source here has no .git,
# so provide a pretend version (consumed by setuptools-scm, which hatch-vcs uses).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs build backend; build tools occasionally needed for
# native wheels. Keep the layer lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (editable install needs the package present).
COPY . .

# Initialize a git repo so hatch-vcs has something to work with, then install
# the project in editable/development mode with ALL runtime extras + dev/test
# dependencies so a developer can run tests, linters, and any project command.
RUN git init -q && git add -A && git -c user.email=dev@example.com -c user.name=dev commit -qm init || true \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
