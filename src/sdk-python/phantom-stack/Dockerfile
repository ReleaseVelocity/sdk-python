FROM python:3.13-slim

# Build tools occasionally needed for native wheels (pyaudio etc. are not installed here)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. The source tree has no .git in the
# container build context, so we initialize a throwaway repo with a tag so the
# editable install can resolve a version.
COPY . /app

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional groups + dev/test tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
