FROM python:3.13-slim

# Version is normally derived from git tags via hatch-vcs.
# This source tree has no git metadata, so provide a pretend version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1

WORKDIR /app

# Build/runtime tooling. git is needed for hatch-vcs metadata handling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves before full copy.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the source tree.
COPY . .

CMD ["python", "-c", "import strands; print('strands ready')"]
