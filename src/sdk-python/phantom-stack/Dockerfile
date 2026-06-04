FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (pulled in by the bidi extra) needs PortAudio headers; also keep a
# compiler around for any source-only wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata step has the
# package tree available; full source is copied next.
COPY src ./src

# Install the project in editable/development mode with the full feature set
# plus the dev/test tooling (pytest, moto, ruff, mypy, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
