FROM python:3.13-slim

# Allow git-based versioning (hatch-vcs) to work
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required by hatch-vcs to determine the package version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies using a placeholder version so hatch-vcs does not
# fail when the .git directory is absent during this cached layer. We install
# the full set of optional groups ("all") plus dev/test tooling so that every
# test in the suite can be collected and run.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the rest of the source (including .git if present) and install editable.
COPY . .

RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
