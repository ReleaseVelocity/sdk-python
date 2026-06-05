FROM python:3.12-slim

# portaudio is required to build pyaudio (bidi extra); git needed for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    gcc \
    g++ \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy full source (hatch-vcs needs git metadata; .git copied via compose volume at runtime,
# but for build we set a fallback version so the editable install succeeds)
COPY . .

# hatch-vcs derives the version from git tags. Provide a fallback so the build
# does not fail when git metadata is unavailable.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the package in editable/development mode with all extras + dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
