FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version for setuptools_scm/hatch-vcs to consume.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install full dependency set (all optional groups + dev/test tools) without
# the project itself yet, so dependency resolution is cached.
RUN pip install --upgrade pip \
    && pip install hatchling hatch-vcs

# Copy the rest of the source.
COPY . .

# Install the project in editable/development mode with all extras + dev tools.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
