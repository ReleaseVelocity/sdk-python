FROM python:3.11-slim

# Avoid interactive prompts and enable cleaner pip output
ENV DEBIAN_FRONTEND=noninteractive \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs determines the version from git tags. The source is not a git repo,
# so we provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev

# System deps:
#  - git: required by hatch-vcs at build/install time
#  - build-essential & portaudio19-dev: needed for optional native deps (pyaudio) if bidi extras are used
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy the full project (build context is the project root)
COPY . .

# Initialize a minimal git repo with a tag so hatch-vcs has a version source,
# then install the package in editable/dev mode with all extras + dev tools.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v0.0.0

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
