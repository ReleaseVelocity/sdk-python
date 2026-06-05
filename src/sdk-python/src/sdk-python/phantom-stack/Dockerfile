FROM python:3.12-slim

# Avoid interactive prompts and ensure stdout is unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# System build deps: some transitive packages (e.g. audio/native libs) may need
# a compiler. portaudio19-dev is required for the optional bidi pyaudio dep.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy only the dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve the package
# during dependency resolution before full source is copied.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain so tests, linters, and type checkers are available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package).
COPY . .

# Re-run editable install to register entry points / metadata against the real
# source tree (fast, dependencies already satisfied).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
