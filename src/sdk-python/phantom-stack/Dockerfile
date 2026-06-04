FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install can resolve
# a version without failing.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs metadata hooks at build time.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install + metadata generation.
COPY src ./src

# Install the project in editable/development mode with all optional model
# providers ("all") plus the dev/test toolchain ("dev"). This mirrors the
# hatch-test environment which uses features = ["all"].
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
