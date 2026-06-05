FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git history, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Editable install with all optional features plus dev/test tooling.
# Copy the package source needed for the editable install resolution.
COPY src ./src

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
