FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow an editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio is required to build/use pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libportaudio2 \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all runtime extras,
# dev/test tooling, and bidirectional-streaming extras.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
