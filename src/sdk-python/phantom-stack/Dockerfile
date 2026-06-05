FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# include git history, provide a fallback version so the editable install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
# - git: hatch-vcs version detection / general tooling
# - build-essential: occasional native builds for transitive deps
# - portaudio19-dev, libasound2-dev: required by pyaudio (bidi extra / audio tests)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install the dependency closure (without the project source) to cache layers.
# We install the full set of optional + dev dependencies for a complete dev env.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

# Re-run editable install to ensure metadata is up to date after full copy.
RUN pip install -e ".[all,dev]"

CMD ["bash"]
