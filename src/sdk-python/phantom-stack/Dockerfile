FROM python:3.12-slim

# hatch-vcs determines version from git tags; without a git repo we must
# provide an explicit version so the build/editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio is needed to build pyaudio (pulled in by the bidi extra). Keep
# build tooling for any native deps that need compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install in editable/dev mode with all optional + dev/test dependencies.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
