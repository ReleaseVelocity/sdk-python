FROM python:3.12-slim

# System deps: git (needed by hatch-vcs for version detection), build tools,
# and portaudio for the optional pyaudio (bidi) dependency.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so create a throwaway repo with a tag so the build can resolve a
# version. This only affects the in-container source tree.
COPY . /app

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "container build" && \
    git tag v0.0.0

# Install the project in editable/dev mode with ALL optional features plus the
# dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
