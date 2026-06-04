FROM python:3.12-slim

# System deps:
# - git: hatch-vcs determines the package version from git tags/metadata
# - portaudio19-dev + gcc: needed to build pyaudio (pulled in by the bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Install project in editable/development mode with ALL extras and dev tools.
# The "all" extra pulls in every model-provider integration; "dev" adds
# pytest, ruff, mypy, moto, hatch, etc.
COPY . .

# hatch-vcs needs git metadata to compute a version. The build context may not
# include a populated .git, so provide a fallback version for the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
