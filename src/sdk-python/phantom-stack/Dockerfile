FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. Since the build context has no
# .git history, provide a pretend version so the build backend succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the actual package).
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev tooling, and test dependencies so a developer can run tests/linters.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
