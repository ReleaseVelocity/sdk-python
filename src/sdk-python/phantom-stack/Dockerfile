FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require a populated git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System libs needed by some optional/dev deps (pyaudio -> portaudio, build tooling)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed because editable install references it)
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
