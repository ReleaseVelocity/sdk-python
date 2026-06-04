FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Source is not a git repo here,
# so provide a pretend version for the build backend to consume.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# git is useful for dev workflows; build tooling kept minimal (wheels available
# for all required deps on slim). Add build-essential only if compilation fails.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# src layout package is needed for the editable install metadata resolution.
COPY src ./src

# Install the project in editable/development mode with all optional model
# providers (all) plus the dev/test toolchain (dev). 'bidi' is intentionally
# excluded as it requires portaudio/pyaudio and Python>=3.12-only deps not in
# the default/all test matrix.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
