FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a fallback version via setuptools_scm's pretend var.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed because hatch-vcs/setuptools_scm probes for it; keep it available
# for developers working inside the container.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-install build backend so the editable install resolves quickly.
RUN pip install --no-cache-dir hatchling hatch-vcs

# Copy the full project source.
COPY . .

# Install in editable/development mode with ALL feature extras and dev tooling
# (pytest, moto, ruff, mypy, etc.) so tests and linters run inside the container.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
