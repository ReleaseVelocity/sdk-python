FROM python:3.12-slim

# System deps: git for hatch-vcs, build tools + portaudio for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. Since the source is shipped
# without git history, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

WORKDIR /app

# Install hatch + uv (development workflow tooling used by the project)
RUN pip install --upgrade pip && pip install hatch uv

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
