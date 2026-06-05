FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs to determine the dynamic version from tags
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: required by pyaudio (bidi extra)
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# The project has no .git here, and hatch-vcs derives the version from git tags.
# Initialize a throwaway repo with a tag so the build can resolve a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v1.0.0

# Copy the rest of the source
COPY . /app

# Re-commit full source so the working tree is clean for hatch-vcs
RUN git add -A && git commit -qm "source" || true

# Install the project in editable/dev mode with ALL optional features and dev tools.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
