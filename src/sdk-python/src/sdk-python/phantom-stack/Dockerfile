FROM python:3.12-slim

# hatch-vcs derives the version from git; provide a fallback so builds
# succeed even when no tag history is present in the build context.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git: required by the hatch-vcs build backend.
# build-essential / portaudio19-dev: cover native builds for optional
# dependencies (e.g. the bidi extra's pyaudio).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project (build context is the repository root).
COPY . .

# The build context has no .git, so create a minimal repo + tag for
# hatch-vcs, then install the package in editable/development mode with
# every optional feature ("all") plus the dev/test toolchain ("dev").
RUN git init -q && \
    git -c user.email=dev@local -c user.name=dev add -A && \
    git -c user.email=dev@local -c user.name=dev commit -q -m "snapshot" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
