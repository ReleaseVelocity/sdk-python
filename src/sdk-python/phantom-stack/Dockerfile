FROM python:3.12-slim

# Tools needed: git is required because the build backend (hatch-vcs)
# derives the package version from git tags. portaudio for pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
COPY src ./src

# hatch-vcs needs a git repo with a tag to compute the version. The real
# source has no .git here, so create a throwaway repo with a version tag.
# Allow overriding the pretend version through a build arg.
ARG PACKAGE_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION}

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag "v${PACKAGE_VERSION}"

# Install the project in editable/development mode with ALL extras plus dev tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
