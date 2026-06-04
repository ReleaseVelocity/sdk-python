FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# git metadata, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs (and pre-commit) expect git to be present
# - portaudio19-dev: required to build pyaudio (pulled by the bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml ./

# Copy the rest of the project.
COPY . .

# Initialize a minimal git repo so hatch-vcs has a clean tree to work with,
# then install the project in editable/development mode with ALL optional
# dependencies plus dev/test tooling.
RUN git init -q && git add -A && git -c user.email=dev@local -c user.name=dev commit -qm init \
    && git tag v0.0.0 \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
