FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Minimal system deps. git is required because the build backend (hatch-vcs)
# derives the package version from git tags. portaudio is needed for the
# pyaudio dependency pulled in by some optional feature sets.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs reads the version from git tags. There is no .git in the build
# context, so initialise a throwaway repo with a tag to provide a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag v0.0.0

# Copy the rest of the source.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Commit the full source so the tagged version resolves cleanly, then install
# the project in editable/development mode with ALL extras + dev tooling.
RUN git add -A && git commit -qm "source" \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
