FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching. The README is
# referenced by pyproject.toml (readme = "README.md") during the build.
COPY pyproject.toml README.md ./

# Pre-install dependencies (editable install needs the source tree, so we do
# the full install after copying the source below). This warms the wheel
# layer for the heavy "all" + "dev" feature sets.
RUN pip install hatchling hatch-vcs

# Copy the rest of the project source.
COPY . .

# Install the project in editable/development mode with ALL optional feature
# sets and the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
