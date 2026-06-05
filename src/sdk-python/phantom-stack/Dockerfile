FROM python:3.12-slim

# portaudio is required to build pyaudio (bidi extra); git is needed for hatch-vcs versioning
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the package source so editable install can resolve packages
COPY src/strands ./src/strands

# Install the project in editable/dev mode with all optional + dev/test dependencies.
# hatch-vcs needs a git repo or pretend version; we use HATCH_VCS_PRETEND_VERSION.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi,bidi-gemini,bidi-openai,dev]"

# Copy the rest of the project (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
