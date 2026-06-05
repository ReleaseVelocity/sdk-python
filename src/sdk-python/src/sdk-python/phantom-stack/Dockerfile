FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo, so provide a fallback version so the editable build succeeds without
# needing git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Build tooling: a few optional deps (e.g. pyaudio for bidi) need system libs.
# Keep it minimal; build-essential covers native compilation if needed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve the package).
COPY src ./src

# hatch-vcs reads the version from git metadata. Initialise a throwaway repo
# with a tag so the build has a concrete version even though the source tree
# was delivered without git history. SETUPTOOLS_SCM_PRETEND_VERSION acts as a
# secondary fallback.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "dev build" \
    && git tag v1.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
