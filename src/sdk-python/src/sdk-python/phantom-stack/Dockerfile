FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. The build
# context has no .git history, so provide a pretend version to satisfy the
# dynamic version resolution during an editable install.
ARG STRANDS_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# pyaudio (from the bidi extra) needs PortAudio headers; build tools cover any
# packages that ship without prebuilt wheels for this platform.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Source must be present before an editable install because the wheel target
# points at src/strands.
COPY src ./src

# Install the project in editable/development mode with every optional feature
# group plus the dev/test tooling (pytest, moto, ruff, mypy, hatch, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands-agents ready:', strands.__file__)"]
