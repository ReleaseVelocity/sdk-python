FROM python:3.12-slim

# hatch-vcs (via setuptools-scm) derives the version from git tags. No git
# history is shipped in this build context, so we initialize a throwaway git
# repo with a tag inside the image purely so the version can be determined.
ARG PKG_VERSION=0.1.0
ENV PKG_VERSION=${PKG_VERSION}

# pyaudio (bidi extra) needs PortAudio headers; build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy the project source needed to build metadata.
COPY pyproject.toml README.md ./
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs can determine the
# version, then install the project editable with all runtime + dev/test deps.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "build" && \
    git tag "v${PKG_VERSION}" && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the source (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
