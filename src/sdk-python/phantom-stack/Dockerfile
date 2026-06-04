FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git metadata, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# PortAudio headers/lib are required to build pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/dev mode with ALL dependency groups
# (runtime extras + dev/test tooling + bidi extras).
RUN pip install --no-cache-dir -e ".[dev,all,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
