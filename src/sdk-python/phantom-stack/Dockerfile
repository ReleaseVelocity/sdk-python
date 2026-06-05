FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the build/editable install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio is required to build pyaudio (pulled in by the bidi extra / all-ish deps)
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package).
COPY src ./src

# Install the project in editable/dev mode with all optional groups + dev tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
