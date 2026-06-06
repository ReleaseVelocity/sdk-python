FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve build backend and project deps (without the package source yet)
# by doing a metadata-only style install once source is present. We keep this
# layer minimal; full install happens after source copy.
RUN pip install --upgrade pip hatchling hatch-vcs

# Now copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain so a developer can run tests and linters.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
