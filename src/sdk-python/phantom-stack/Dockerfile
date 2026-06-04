FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include a .git directory, provide a fallback version so the editable install
# of strands-agents succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System dependencies:
#  - git: hatch-vcs version discovery (and general dev workflows)
#  - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extras)
#  - build-essential: native extension compilation fallback
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Upgrade tooling and install the project in editable/dev mode with all
# optional model providers plus the dev/test toolchain.
RUN pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
