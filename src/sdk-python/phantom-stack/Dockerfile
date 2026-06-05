FROM python:3.12-slim

# System deps:
# - git: hatch-vcs / general tooling
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: required to build pyaudio (used by the optional 'bidi' extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. This project has no .git in the
# workspace, so provide a pretend version so the editable install can build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed by the editable install / build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev (test/lint) dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the test suites and remaining project files.
COPY tests ./tests
COPY tests_integ ./tests_integ

CMD ["sleep", "infinity"]
