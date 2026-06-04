FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Source is not a git repo here,
# so provide a pretend version for setuptools_scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is needed for pyaudio (pulled in by the bidi extra, part of "all").
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed because the install is editable / dev-mode).
COPY src ./src

# Install the project in editable/development mode with ALL deps + dev/test deps.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
