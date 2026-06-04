FROM python:3.12-slim

# Build args for version (project uses hatch-vcs which needs git tags;
# since .git may be absent we provide a pretend version)
ARG STRANDS_VERSION=0.0.0+local

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}

# portaudio19-dev + gcc needed for optional bidi extra (pyaudio); git useful for tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install / metadata
COPY src ./src

# Install the project in editable/development mode with all + dev (test) deps.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
