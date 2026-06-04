FROM python:3.12-slim

# System deps: git is required by hatch-vcs to determine the version from tags.
# build-essential is needed for any packages that need compilation (e.g. pyaudio for bidi, though bidi excluded).
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full source
COPY . /app

# hatch-vcs derives the version from git tags. The project source is copied
# without a .git directory, so initialize a repo and create a tag so the
# build backend can compute a valid version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerized build" && \
    git tag v0.0.0

# Upgrade pip and install the project in editable/development mode with all
# optional features plus dev/test tooling.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
