FROM python:3.12-slim

# Avoid interactive prompts and ensure unbuffered output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Install git (needed by hatch-vcs to determine version from git tags) and
# build tooling for any native deps (e.g. pyaudio for bidi extras is optional).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# hatch-vcs derives the package version from git metadata. Provide an explicit
# fallback version so the build backend does not fail when no git tags exist.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

COPY . /app

# Initialize a git repo with a version tag if one is not present, so hatch-vcs
# can resolve a version during the editable install.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app \
        && git -C /app config user.email "dev@example.com" \
        && git -C /app config user.name "dev" \
        && git -C /app add -A \
        && git -C /app commit -q -m "container build" \
        && git -C /app tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL runtime extras plus
# the dev toolchain (pytest, ruff, mypy, moto, etc.) so the container is fully
# usable for development and testing.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
