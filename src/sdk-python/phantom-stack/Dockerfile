FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context has no
# git repository, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System dependencies:
# - git: useful for dev workflows / version control inside the container
# - build-essential: in case a transitive dependency needs to compile
# - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra),
#   matching the project's CI environment
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Build backend + dev workflow tool used by the project.
RUN pip install --no-cache-dir hatchling hatch-vcs hatch

# Copy the full source so the editable install can find the package.
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, mypy, ruff, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
