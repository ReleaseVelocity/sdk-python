FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the dynamic version hook.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+docker \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System libraries:
#  - git: hatch-vcs falls back to git metadata when present
#  - build-essential / portaudio / libasound2: required to build native deps
#    (e.g. pyaudio) used by the optional bidi extras and full dev tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package so the editable install can resolve the package tree.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
