FROM python:3.12-slim

# hatch-vcs (backed by setuptools_scm) normally derives the version from git
# tags. There is no .git tree in this build context, so we provide the version
# explicitly via the setuptools_scm pretend-version env var. This avoids needing
# git or any apt packages at build time (the build network has no DNS).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the project source.
COPY . .

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
