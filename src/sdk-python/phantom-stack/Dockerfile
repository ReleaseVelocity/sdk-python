FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Build tools needed for some native deps (e.g. pyaudio for bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags; provide a fallback so the
# editable install does not fail when no .git / tags are present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the rest of the source.
COPY . .

# Initialize a git repo with a tag so hatch-vcs can resolve a version,
# then install the package in editable/dev mode with all + dev + bidi extras.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "containerize" \
    && git tag v0.0.0 \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

CMD ["sleep", "infinity"]
