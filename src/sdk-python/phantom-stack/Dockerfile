FROM python:3.12-slim

# System deps: git is required because the project uses hatch-vcs (version from git tags),
# build tools for any native deps (e.g. pyaudio for bidi requires portaudio).
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs needs git metadata to derive a version. Initialize a git repo with a
# tag so editable install works even without the original .git directory.
COPY . .
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/dev mode with ALL optional features + dev/test deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
