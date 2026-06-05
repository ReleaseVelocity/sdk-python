FROM python:3.12-slim

# Provide a fallback version for hatch-vcs since the source tree is not a git repo.
# hatch-vcs reads SETUPTOOLS_SCM_PRETEND_VERSION_FOR_<NORMALIZED-DIST-NAME>.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Build tools needed by some native deps (e.g. pyaudio for the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the entire project (needed because hatch-vcs reads git metadata and the
# editable install references tests/configs).
COPY . .

# hatch-vcs derives the version from git. The source tree is not a git repo,
# so initialize a throwaway repo with a tag to give it a deterministic version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional features
# and the dev (test/lint) tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]" \
    && pip install pytest-asyncio pytest-xdist pytest-cov moto

CMD ["sleep", "infinity"]
