FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the dynamic version hook.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+local \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+local \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+local
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (build will still re-run editable install after
# full source copy, but this warms the layer cache for heavy deps).
RUN pip install --upgrade pip

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
