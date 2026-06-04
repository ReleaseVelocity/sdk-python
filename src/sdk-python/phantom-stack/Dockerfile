FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context may not be a
# git repo, so provide a pretend version so the editable install succeeds.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

# PyAudio (bidi extra) needs PortAudio headers; git is useful for tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install metadata.
COPY src ./src

# Install the project in editable/development mode with all extras plus the
# dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
