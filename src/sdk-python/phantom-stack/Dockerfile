FROM python:3.12-slim

# PortAudio headers/libs are needed for pyaudio (bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the package version from git tags. This source tree has no
# .git directory, so supply the version explicitly via hatch-vcs's env var.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling needed for the hatchling/hatch-vcs build backend.
RUN pip install --no-cache-dir --upgrade pip hatchling hatch-vcs

# Copy the rest of the project source.
COPY . .

# Install the project in editable/dev mode with all runtime + dev + bidi extras.
RUN pip install --no-cache-dir -e ".[dev,all,bidi-all]"

CMD ["sleep", "infinity"]
