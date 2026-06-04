FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies using the manifest. We create a minimal package
# layout so the editable install during the source copy step is fast.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Now copy the full project source.
COPY . /app

# Reinstall to pick up the real package metadata/entry points.
RUN pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
