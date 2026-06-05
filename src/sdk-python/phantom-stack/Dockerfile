FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all runtime + optional + dev dependencies in editable mode.
# The src layout package is created in a later layer; install deps without the
# project itself first, then editable-install the project once source is present.
COPY src ./src

RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
