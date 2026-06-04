FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is required by hatch-vcs/build tooling; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy project metadata first for better layer caching.
COPY pyproject.toml README.md ./
# Source is needed because the wheel target packages src/strands and the
# editable install resolves the package layout from it.
COPY src ./src

# Install the project in editable/development mode with dev + all extras so a
# developer can run tests, linters, and any project command.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
