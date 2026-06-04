FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs to derive the package version from tags
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: required header for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source tree.
COPY . /app

# hatch-vcs reads the version from git metadata. The build context has no
# .git directory, so create a throwaway repo with a tag to give it a version.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app config user.email "dev@example.com" && \
        git -C /app config user.name "dev" && \
        git -C /app add -A && \
        git -C /app commit -q -m "containerized build" && \
        git -C /app tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
