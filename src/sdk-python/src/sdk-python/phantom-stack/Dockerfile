FROM python:3.13-slim

# System deps: git (for any vcs operations), build tools for native wheels,
# and portaudio for pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, provide an explicit pretend version so the build
# backend can resolve a version without git.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
