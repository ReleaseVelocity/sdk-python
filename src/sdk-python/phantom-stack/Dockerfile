FROM python:3.12-slim

# hatch-vcs determines version from git tags. Since the build context may not be
# a git repo, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is needed for the optional pyaudio dependency (bidi extras); install
# build tooling for any packages that need native compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for the editable install metadata.
COPY src ./src

# Install the project in editable/development mode with all runtime + dev deps.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
