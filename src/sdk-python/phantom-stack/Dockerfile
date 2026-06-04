FROM python:3.12-slim

# hatch-vcs needs a version; no .git is shipped, so provide a pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is needed for pyaudio (bidi extra); build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create source package dir so editable install metadata resolves.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev/test deps.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project.
COPY . .

CMD ["sleep", "infinity"]
