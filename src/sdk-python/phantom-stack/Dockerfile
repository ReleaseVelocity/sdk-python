FROM python:3.12-slim

# hatch-vcs derives the version from git tags; this repo has no git history,
# so provide a fallback version via setuptools-scm's pretend-version override.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
#  - git: required by hatch-vcs / setuptools-scm version backend
#  - portaudio19-dev, libasound2-dev: required to build pyaudio (bidi extra)
#  - gcc/build-essential: native builds for some transitive deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and
# hatchling reads the package layout at build time).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# all bidi features, and the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__version__ if hasattr(strands,'__version__') else 'ok')"]
