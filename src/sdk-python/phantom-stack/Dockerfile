FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in this
# workspace, so provide a pretend version that hatch-vcs/setuptools-scm honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# pyaudio (pulled in by the bidi extras) needs PortAudio headers to build.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy the full source tree (needed for the editable install to resolve packages).
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
