FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The shipped source tree has no
# .git directory, so a throwaway repo with a tag is created below to satisfy it.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs / general dev workflows
# - portaudio19-dev + gcc: required to build pyaudio (pulled by the bidi extras
#   which are part of the full optional dependency set)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project (source, tests, configs, manifests).
COPY . .

# Provide git metadata so hatch-vcs can resolve a version for the editable install.
RUN git init -q && git add -A \
    && git -c user.email=dev@local -c user.name=dev commit -qm "build" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
