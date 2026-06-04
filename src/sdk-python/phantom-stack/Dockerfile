FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree in this
# environment has no git history, so provide an explicit version to the
# setuptools-scm/hatch-vcs machinery used by the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling).
COPY src ./src

# Install the project in editable/development mode with the full set of
# optional providers ("all") plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
