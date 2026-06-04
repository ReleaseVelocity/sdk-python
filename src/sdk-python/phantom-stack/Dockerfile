FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio for the bidi extra) need PortAudio + build tools.
# Install minimal system deps for native builds and audio bindings.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package layout so the editable install metadata resolves,
# then install the full dependency set. We install in editable mode with all
# optional extras plus dev/test tooling so a developer can run tests & linters.
COPY src ./src

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
