FROM python:3.12-slim

# System deps:
# - git: required because the build backend (hatch-vcs) derives the package
#   version from git tags. We init a repo + tag at build time.
# - build-essential: some transitive deps (e.g. pyaudio for the bidi extra) may
#   need a compiler/headers.
# - portaudio19-dev: headers for pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git history with a tag to compute the version. Without it,
# the build fails. Create a throwaway repo and tag so versioning succeeds.
RUN git init -q \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "containerize" \
    && git tag v1.0.0

# Install the project in editable/dev mode with ALL optional + dev dependencies
# so tests, linters, and every model provider are available.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
