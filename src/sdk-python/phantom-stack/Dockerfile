FROM python:3.13-slim

# Install git (hatch-vcs needs git history for versioning) and build basics
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        portaudio19-dev \
        gcc \
        g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source and git metadata (hatch-vcs derives version from git tags)
COPY . .

# hatch-vcs requires a version; if no git tags exist, provide a fallback
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Install the project in editable/dev mode with all + dev dependencies
RUN pip install --no-cache-dir -e ".[all,dev,bidi]"

CMD ["sleep", "infinity"]
