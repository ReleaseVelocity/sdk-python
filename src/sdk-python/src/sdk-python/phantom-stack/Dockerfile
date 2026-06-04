FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version (hatch-vcs honors SETUPTOOLS_SCM_PRETEND_VERSION).
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most deps ship wheels. git kept available for tooling/pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves during dep install.
RUN mkdir -p src/strands && touch src/strands/__init__.py

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package).
COPY . .

# Re-run editable install to register the real package metadata/entry points.
RUN pip install -e ".[all,dev]"

CMD ["bash", "-lc", "python -m pytest --co -q && tail -f /dev/null"]
