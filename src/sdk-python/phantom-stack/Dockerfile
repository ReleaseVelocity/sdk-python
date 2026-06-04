FROM python:3.12-slim

# hatch-vcs (setuptools_scm based) needs a version; project is shipped without a
# .git dir in this environment, so supply a pretend version for the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio for the bidi extra) need PortAudio/build tools.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all runtime + dev/test dependencies in editable mode.
# Copy the rest of the source then install editable.
COPY . .

RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
