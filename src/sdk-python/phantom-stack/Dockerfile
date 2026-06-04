FROM python:3.12-slim

# System deps:
# - git: hatch-vcs derives the package version from git tags
# - build-essential: in case any wheel needs native compilation
# - portaudio19-dev: pyaudio (bidi extra) requires PortAudio headers
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full source tree (needed for editable install + tests)
COPY . .

# hatch-vcs determines the version from git tags. The source tarball is not a
# git repo, so initialize one and tag it to give hatchling a valid version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@local" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "containerize" && \
        git tag v0.0.0 ; \
    fi

# Upgrade pip tooling and install the project in editable/development mode
# with ALL optional dependencies and the dev/test toolchain.
RUN python -m pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
