# Strands Agents Python SDK - development image
# Project requires Python >=3.10; use a slim 3.12 base.
FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because the build backend (hatch-vcs) derives the version
# from git tags. portaudio headers are needed if pyaudio (bidi extra) builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . .

# hatch-vcs needs a git repo with a tag to compute the package version.
# The real repo has no .git here, so create a throwaway one with a tag.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "container build" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with all optional and dev
# dependencies so a developer can run tests, linters, and any project command.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
