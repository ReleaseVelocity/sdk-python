FROM python:3.12-slim

# hatch-vcs (setuptools_scm under the hood) derives the version from git tags.
# There is no .git in the build context, so provide a pretend version so the
# build backend can resolve a version during the editable install.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# - git: hatch-vcs / general tooling
# - gcc/g++: build native wheels when no prebuilt wheel is available
# - portaudio19-dev: required to build pyaudio (pulled in via bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# The package source is needed for the editable install to resolve the package.
COPY src ./src

# Install the project in editable/development mode with all optional provider
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
