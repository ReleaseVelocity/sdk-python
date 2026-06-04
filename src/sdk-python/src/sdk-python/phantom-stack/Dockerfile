FROM python:3.12-slim

# System deps:
# - git: hatch-vcs derives the package version from git tags/history
# - build-essential & portaudio: needed for some optional native deps (pyaudio in bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with a tag to determine the version. The project
# is delivered without a .git directory, so we bootstrap a minimal repo with a
# version tag. HATCH_VCS_PRETEND_VERSION is also honored as a fallback.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the full source tree.
COPY . .

# Initialize a git repo so hatch-vcs (and editable installs) have version info.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "dev snapshot" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional deps + dev tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
