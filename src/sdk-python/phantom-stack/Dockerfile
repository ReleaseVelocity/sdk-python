FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to let the editable install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio headers are required to build pyaudio (pulled in by the bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with the full dependency set:
# all model providers + a2a + bidi + dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
