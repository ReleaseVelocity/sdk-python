FROM python:3.12-slim

# System deps: git (hatch-vcs needs git history for versioning),
# portaudio for the optional pyaudio (bidi) dependency, and build tools
# in case any wheel needs compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves,
# then copy the full source.
COPY . .

# hatch-vcs derives the version from git tags. The build context may not
# carry full git history, so provide a fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
