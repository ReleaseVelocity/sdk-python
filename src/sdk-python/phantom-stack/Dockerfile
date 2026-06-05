FROM python:3.12-slim

# hatch-vcs derives version from git tags. The build context is not a git repo,
# so provide a pretend version to allow the build/install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest + readme first for better layer caching.
# pyproject uses dynamic version via VCS, so SETUPTOOLS/hatch needs the value above.
COPY pyproject.toml README.md ./

# Copy source so editable install resolves the package.
COPY src ./src

# Install the project in editable/dev mode with all optional deps + dev tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
