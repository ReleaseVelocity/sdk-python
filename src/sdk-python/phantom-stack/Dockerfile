FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git metadata, provide a fallback version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Minimal system deps:
# - git: hatch-vcs version source / pre-commit
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# dev tools, and the bidi extras so every project command is available.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
