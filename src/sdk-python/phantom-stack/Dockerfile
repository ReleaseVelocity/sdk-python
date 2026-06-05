FROM python:3.11-slim

# hatch-vcs/setuptools-scm derives the version from git tags. There is no git
# history in this build context, so provide a pretend version to allow the
# editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# System dependencies:
#  - git: required by hatch-vcs build backend
#  - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra)
#  - build-essential: native compilation for some deps (pyaudio)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir referenced by the wheel target so the editable
# install metadata step can resolve packages before full source is copied.
RUN mkdir -p src/strands

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev toolchain (pytest, mypy, ruff, moto, ...).
# The 'bidi' extras pull in pyaudio which needs the system audio libs above.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
