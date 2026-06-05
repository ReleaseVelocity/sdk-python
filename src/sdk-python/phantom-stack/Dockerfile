FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs to derive the package version from tags
# - build-essential & portaudio: needed by some optional deps (e.g. pyaudio in bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_DEFAULT_TIMEOUT=120 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project source
COPY . /app

# hatch-vcs derives the version from git tags. The project is copied without a
# .git directory, so initialize a throwaway repo with a version tag so that the
# editable install can compute a valid version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "snapshot" && \
        git tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
