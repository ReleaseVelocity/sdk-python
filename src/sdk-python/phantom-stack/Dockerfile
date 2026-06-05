FROM python:3.12-slim

# Avoid interactive prompts and reduce noise
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the project uses hatch-vcs (versioning from git tags)
# build-essential / portaudio for optional native deps (pyaudio in bidi)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with at least one tag to compute the version.
# Initialize a throwaway git repo and tag so the editable install succeeds.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v0.0.0

# Copy the rest of the source
COPY . /app

# Re-commit full source so hatch-vcs sees a clean tree on the tag
RUN git add -A && git commit -qm "src" --allow-empty

# Install the project in editable/dev mode with all extras + dev tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
