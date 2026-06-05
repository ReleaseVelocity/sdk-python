FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version to allow the editable build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Default AWS env so boto3/moto-based tests have a region without real creds.
ENV AWS_DEFAULT_REGION=us-west-2

WORKDIR /app

# System build deps: a few transitive packages may need a compiler.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves during dep step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies in editable mode.
# Note: the "bidi" extra is intentionally excluded because pyaudio requires
# system PortAudio headers and is not part of the "all" feature set.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the full source tree (overwrites placeholder package).
COPY . .

# Re-run editable install so console scripts / metadata reflect real source.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
