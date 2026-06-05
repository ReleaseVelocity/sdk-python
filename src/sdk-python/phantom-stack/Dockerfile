FROM python:3.13-slim

# System deps:
# - git: hatch-vcs derives the version from git tags/metadata
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
# - build-essential: occasional native builds for transitive deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project. hatch-vcs needs the .git metadata to compute a version;
# if it's unavailable we fall back to a pinned version via SETUPTOOLS_SCM-style env.
COPY . /app

# Provide a fallback version so hatch-vcs/hatchling does not fail when building
# in editable mode without git tags available.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_NO_HOOKS=false \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

# Upgrade pip tooling, then install the project in editable mode with the full
# dependency surface: runtime + all model providers + bidi + dev/test tooling.
RUN python -m pip install --upgrade pip setuptools wheel hatchling hatch-vcs \
    && pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
