FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here is not a
# git checkout, so provide an explicit version to the build backend.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package directory so editable install metadata resolves.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
