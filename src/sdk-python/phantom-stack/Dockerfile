FROM python:3.12-slim

# Version cannot be derived from git tags (no .git in build context),
# so provide a pretend version for hatch-vcs.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - portaudio19-dev: required to build pyaudio (bidi extra)
# - build-essential: native compilation for some wheels
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install resolution)
COPY src ./src

# Install the project in editable/development mode with all dependency groups.
# "all" pulls every model provider extra; "dev" adds test+lint tooling.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
