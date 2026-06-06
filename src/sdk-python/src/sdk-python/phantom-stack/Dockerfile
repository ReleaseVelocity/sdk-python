FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; git is needed because the
# project derives its version from VCS via hatch-vcs.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines the version from git tags. There is no git history in
# this build context, so provide a pretend version to satisfy the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata generation).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# the dev tooling, and the bidi extras so every project command is available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
