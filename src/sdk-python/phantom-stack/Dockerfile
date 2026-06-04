FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to allow the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# pyaudio (bidi extra) needs portaudio headers + a compiler for some native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves.
RUN mkdir -p src/strands

# Copy the full source.
COPY . /app

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
