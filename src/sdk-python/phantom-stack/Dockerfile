FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Since the build context may not
# include a git repo, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps: git (hatch-vcs fallback / tooling), build tools for any native wheels,
# and portaudio for the optional bidi pyaudio dependency.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package directory so the editable install can resolve the package
# before the full source is copied (improves caching for dependency installs).
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies in editable mode.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the full source tree.
COPY . .

# Reinstall editable to ensure metadata reflects the full package after source copy.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
