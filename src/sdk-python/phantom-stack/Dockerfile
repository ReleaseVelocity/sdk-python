FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the source tree here has no git
# history, so provide a pretend version so the build backend can resolve it.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# System deps:
#  - git: hatch-vcs version source (kept available for dev workflows)
#  - build-essential: in case any dependency needs to compile
#  - portaudio19-dev: required by pyaudio (bidi extra / dev tooling)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable mode with all runtime extras plus dev/test deps.
# We need the source present for an editable install, so copy it before installing.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (docs, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
