FROM python:3.12-slim

# Avoid interactive prompts and ensure stdout is unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
#  - git: hatch-vcs derives the version from git tags/history
#  - build-essential: in case any dependency needs to compile a native extension
#  - portaudio19-dev: pyaudio (pulled in by bidi extras) needs PortAudio headers
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install of the package)
COPY . .

# hatch-vcs requires a git repo with at least one commit to determine the version.
# If the build context has no .git, initialize a throwaway repo with a version tag.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "snapshot" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
