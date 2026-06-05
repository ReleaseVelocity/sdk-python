FROM python:3.12-slim

# hatch-vcs derives the version from git tags; this repo has no git history in
# the build context, so provide a pretend version to let the build succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for an editable install with hatchling).
COPY . .

# Install the project in editable/development mode with ALL extras plus the
# dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
