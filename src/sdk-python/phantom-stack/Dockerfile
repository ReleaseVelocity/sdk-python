FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs determines the version from git tags. This repo may have no tags
# inside the build context, so provide a pretend version as a fallback.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

# Install minimal system build deps. portaudio is needed only for the optional
# 'bidi' (pyaudio) feature which we are NOT installing, so keep it lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project (source, tests, git metadata for versioning)
COPY . .

# Install the project in editable/development mode with all runtime extras
# (everything except the bidi audio stack) plus the dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["bash"]
