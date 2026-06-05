FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git directory, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs/version tooling expectations
# - portaudio19-dev + gcc: needed if pyaudio (bidi extra) ever gets built
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the manifest first, install deps, then copy source.
COPY pyproject.toml README.md ./
COPY src/strands/__init__.py src/strands/__init__.py

# Editable install with all model providers + dev/test tooling.
RUN pip install -e ".[all,dev]"

# Now copy the rest of the project source.
COPY . .

# Reinstall to pick up the full package metadata in editable mode.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
