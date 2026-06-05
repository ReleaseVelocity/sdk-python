FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by some tooling; build-essential occasionally needed for native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package source path referenced by the wheel target so an
# editable install can resolve before the full source is copied.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project source.
COPY . .

CMD ["sleep", "infinity"]
