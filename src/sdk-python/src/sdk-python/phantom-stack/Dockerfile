FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# contain a .git directory, tell setuptools_scm (used by hatch-vcs) a version
# so the editable install of the package succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git (hatch-vcs fallback) and build tools for any native wheels
# (e.g. pyaudio in the bidi extra needs portaudio headers).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and hatchling
# reads the package layout / version at build time).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev + bidi extras so every dependency and dev tool is available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
