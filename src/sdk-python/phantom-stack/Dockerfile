FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide an explicit version so the build succeeds.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# git is useful for dev workflows / pre-commit; build tooling for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL extras + dev tooling.
# Copy the package source needed for the editable install metadata.
COPY src/strands ./src/strands

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
