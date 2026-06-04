FROM python:3.12-slim

# hatch-vcs (backed by setuptools-scm) derives the version from git tags.
# Provide an explicit fallback version so the build succeeds even without the
# project's git history present in the build context.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Audio bindings (pyaudio, used by the optional bidi extras) need portaudio
# headers + a compiler. Install minimal build deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, etc.).
COPY . .

# Initialize a minimal git repo with a version tag so hatch-vcs can resolve a
# real version at runtime/rebuild without relying on the pretend-version env.
RUN git init -q && git config user.email dev@local && git config user.name dev \
    && git add -A && git commit -qm "dev" && git tag v0.0.0

CMD ["sleep", "infinity"]
