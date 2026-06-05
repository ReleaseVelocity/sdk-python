FROM python:3.12-slim

# hatch-vcs derives the version from git tags. We supply that metadata during
# the build by initializing a throwaway git repo with a tag (see below).
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio for the bidi extra) need build tooling /
# system libs. Install them so the full "[all]" + "[dev]" extras resolve.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Bring in the rest of the project (tests, configs, docs).
COPY . .

# hatch-vcs (setuptools-scm) derives the version from git metadata. The build
# context has no .git, so create a minimal repo with a tag for a clean install.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && git commit -qm "build" && \
    git tag v0.0.0

# Install the project in editable/development mode with all + dev extras.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
