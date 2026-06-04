FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repo, so provide a pretend version to allow the build/install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-fetch dependencies based on the manifest (editable install of just deps).
# We need the package source for an editable install, so copy src too.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test toolchain. The "bidi" extra is intentionally excluded because it
# requires native PortAudio (pyaudio) and is excluded from the test suite and
# linting config in pyproject.toml.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
