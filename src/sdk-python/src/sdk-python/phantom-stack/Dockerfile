FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / pre-commit; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies using a stub package tree so the heavy dependency
# layer can be cached independently from the source code.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install -e ".[all,dev]"

# Now copy the full source tree (overrides the stub).
COPY . .

# Reinstall in editable/dev mode against the real sources.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
