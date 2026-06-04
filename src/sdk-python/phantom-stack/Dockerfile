FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# directory, so provide a pretend version to let the editable install succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package source path referenced by the wheel target so the
# editable install can resolve packages during dependency resolution.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies and the dev/test toolchain.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
