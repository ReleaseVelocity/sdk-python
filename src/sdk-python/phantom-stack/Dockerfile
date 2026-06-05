FROM python:3.12-slim

# hatch-vcs derives version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
# - git: required by hatch-vcs / general dev workflow
# - build-essential, portaudio19-dev, libasound2-dev: needed to build pyaudio
#   for the optional `bidi` feature; included so the full dev toolset works.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

# hatch is the project's development workflow manager (uses uv as installer)
RUN pip install --no-cache-dir hatch uv

WORKDIR /app

# Layer caching: copy manifest first, install deps, then copy source.
COPY pyproject.toml README.md ./

# Pre-resolve/install all runtime + dev dependencies. We install the project
# in editable mode with the "all" and "dev" extras so every dependency
# (including test/lint tooling) is available.
COPY src ./src
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the rest of the project (tests, configs, docs)
COPY . .

# Reinstall editable to ensure metadata is consistent with full source tree
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
