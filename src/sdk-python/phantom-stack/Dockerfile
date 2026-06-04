FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This project copy has no git
# history, so provide a pretend version to allow the build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
