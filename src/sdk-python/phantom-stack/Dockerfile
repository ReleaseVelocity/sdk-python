FROM python:3.12-slim

# System deps: git is required for hatch-vcs versioning; build tools for some native deps (pyaudio/portaudio optional)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy sources (hatch-vcs version provided via HATCH_VCS_PRETEND_VERSION below)
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Ensure hatch-vcs can resolve a version even without tags
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
