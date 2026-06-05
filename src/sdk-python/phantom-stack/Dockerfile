FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not
# contain git history, so provide a pretend version to satisfy the backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install resolves the package
# during the dependency-resolution layer (full source copied below).
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL optional
# features plus the dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package).
COPY . .

# Re-run the install so console scripts / metadata reflect the real tree.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
