FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since we install from a source
# copy (no .git history), provide a pretend version so the build can proceed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs PortAudio headers; git lets hatch-vcs work too.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
