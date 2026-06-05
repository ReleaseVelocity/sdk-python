FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1
ENV LOG_LEVEL=DEBUG

# System dependencies:
#  - git: hatch-vcs / general tooling
#  - portaudio19-dev, libasound2-dev: required to build pyaudio (bidi extra)
#  - build-essential: native compilation fallback for some wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with the full set of
# optional dependencies plus dev and bidi tooling so that every test and
# dev command is available inside the container.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
