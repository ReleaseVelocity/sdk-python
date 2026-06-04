FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no .git directory, so provide a pretend version that hatchling/hatch-vcs will
# use instead. Overridable at build time.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# pyaudio (pulled in by the bidi extra) needs portaudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching. Editable install
# requires the build backend to read packages, so we copy the project metadata
# and README referenced by pyproject before installing.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install.
COPY src ./src

# Install the SDK in editable/development mode with the full dependency set:
# - "dev"  => pytest, ruff, mypy, moto, hatch, pre-commit, etc.
# - "all"  => every model-provider / optional integration dependency.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Bring in tests and remaining project files.
COPY . .

CMD ["python", "-c", "import strands; print('strands import OK', strands.__file__)"]
