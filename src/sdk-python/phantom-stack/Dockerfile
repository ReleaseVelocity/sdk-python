FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context may not be a
# git repo (or have tags), so provide a pretend version to satisfy the build.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio under bidi) and native wheels may need build
# tools / system libs. portaudio is required by pyaudio.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching. The package version
# is dynamic (vcs) and the wheel build only needs src/strands present.
COPY pyproject.toml README.md ./

# Install all runtime + optional + dev/test dependencies in editable mode.
# We pre-create the package dir so the editable build backend is satisfied
# before the full source is copied in.
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full project source.
COPY . .

# Re-run editable install to pick up entry points / metadata against real source.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
