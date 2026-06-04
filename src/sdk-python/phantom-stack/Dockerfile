FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain git metadata, provide a pretend version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: portaudio for pyaudio (bidi extras), build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed because editable install resolves the package + version).
COPY src/ ./src/

# Install the project in editable/development mode with all optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
