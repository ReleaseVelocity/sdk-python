FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# hatch-vcs derives the package version from git tags. The build context has no
# .git, so provide an explicit pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source so editable install + version detection works
COPY src ./src

# Install the project in editable/dev mode with ALL optional deps + dev tools
RUN pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
