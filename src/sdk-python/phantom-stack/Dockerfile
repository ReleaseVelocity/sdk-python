FROM python:3.13-slim

# System deps:
# - git: hatch-vcs reads version from tags (fallback handled via pretend version)
# - build-essential: in case any dependency needs compilation
# - portaudio19-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install (hatchling needs the package present).
COPY src ./src

# Install the project in editable/development mode with all dependencies,
# all model-provider extras, dev/test tooling, and the experimental bidi extras.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
