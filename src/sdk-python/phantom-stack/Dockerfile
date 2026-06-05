FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repo, so provide a pretend version to let the editable install succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve packages.
COPY src/ ./src/

# Install the project in editable/development mode with all optional model
# providers ("all") plus the dev/test toolchain ("dev"). This pulls in pytest,
# ruff, mypy, moto, etc. so a developer can run tests and linters in-container.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
