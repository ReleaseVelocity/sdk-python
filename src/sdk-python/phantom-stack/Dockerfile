FROM python:3.12-slim

# pyaudio (bidi extras) needs portaudio headers; build tools for any native deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The build context has no git
# metadata, so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching, then install.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
