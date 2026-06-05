FROM python:3.13-slim

# System deps: portaudio for the optional `bidi` (pyaudio) extra; build tools for any
# native wheels that lack prebuilt binaries.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree is not a git repo,
# so provide an explicit version to the build backend instead.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL extras and dev tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
