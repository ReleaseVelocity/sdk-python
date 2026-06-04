FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide an explicit pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Some optional dependencies (e.g. pyaudio) need PortAudio headers; build tools
# may be required for any package lacking a wheel on this platform.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Editable install with dev tooling and all optional model-provider extras.
# The package source isn't present yet, so install deps then re-install in
# editable mode once the full tree is copied.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

# Re-run editable install in case metadata changed after full copy.
RUN pip install -e ".[dev,all]"

CMD ["bash"]
