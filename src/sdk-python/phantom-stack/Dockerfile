FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here has
# no .git, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build deps occasionally needed for native wheels (e.g. pyaudio); install only
# the lightweight git client used by some tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install runtime + dev + all optional dependencies in editable mode.
# We pre-create the package dir so editable install resolves the package path.
COPY src/ ./src/
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
