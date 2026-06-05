# Strands Agents Python SDK - development image
# Uses Python 3.12 to support all optional features (incl. bidi which needs >=3.12)
FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs reads the version from git tags; provide a fallback so the
    # build does not require a populated .git history.
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps:
# - git: hatch-vcs version resolution
# - portaudio19-dev + gcc/build tools: required by pyaudio (bidi feature)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libportaudio2 \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves during dep step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies in editable mode.
# Initialize a throwaway git repo so hatch-vcs always resolves a version.
RUN git init -q && git add -A && git -c user.email=a@b.c -c user.name=ci commit -qm init \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the full project source.
COPY . .

# Re-run editable install against the real source tree (ensures metadata + entry points).
RUN git add -A && git -c user.email=a@b.c -c user.name=ci commit -qm src 2>/dev/null || true \
    && pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
