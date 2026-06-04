FROM python:3.13-slim

# Allow overriding the SDK version reported by hatch-vcs (project uses git tags)
ARG STRANDS_VERSION=0.0.0

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required because the build backend (hatch-vcs) derives the package
# version from git tags. portaudio headers are needed for the optional pyaudio
# (bidi) dependency.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / version discovery)
COPY src ./src

# hatch-vcs needs a git repository with a tag to determine the version.
# Initialise a throwaway repo and tag it so the install succeeds deterministically.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag "v${STRANDS_VERSION}"

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
