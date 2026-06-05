FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so provide a pretend version to allow the build/editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all optional dependencies + dev/test tooling in editable mode.
# Copy the full source needed for editable install metadata.
COPY src ./src

RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
