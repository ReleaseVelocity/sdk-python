FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the source tree has no git
# metadata, so provide an explicit fallback version for the build.
# hatch-vcs wraps setuptools-scm; both pretend-version vars are set so the
# build does not require git metadata to resolve the package version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Build/runtime system deps:
# - git: hatch-vcs/hatchling tooling expectations
# - portaudio19-dev: required to build pyaudio (bidi extras / experimental audio io)
# - build-essential: native builds for some optional providers
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src/ ./src/

# Install the project in editable/development mode with ALL dependencies,
# including dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
