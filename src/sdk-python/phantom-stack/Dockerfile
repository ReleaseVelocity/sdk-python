FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide a fallback version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_BUILD_HOOKS_ENABLE=false
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with all runtime extras
# plus dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
