FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps:
#  - git: hatch-vcs version backend reads git metadata
#  - portaudio19-dev + gcc: needed if pyaudio (bidi extra) ever compiled; kept minimal
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create source package dir so editable install metadata works,
# then install all runtime + optional + dev dependencies.
COPY src ./src

# Install project in editable/development mode with all extras + dev tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
