FROM python:3.12-slim

# hatch-vcs derives version from git tags; provide a fallback since the
# build context may not contain git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# portaudio19-dev is needed by pyaudio (bidi extra); build tools for any
# native deps. Keep minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install (package metadata resolution).
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
